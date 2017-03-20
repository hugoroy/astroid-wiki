The [[poll.sh|Polling]] script can contain various functionality for, e.g., processing incoming mail. Here are some examples of that.

## Checking internet connectivity
You can check for internet connectivity before fetching mail. This may prevent e.g. `offlineimap` spending a long time figuring out there is no connection. At the top of the poll script:
~~~bash
# check if we have a connection
if ! ping -w 1 -W 1 -c 1 mail.google.com; then
    echo "there is no internet connection"
    exit
fi
~~~

Similarly, you can check that other things in the environment are ready for receiving mail, e.g. that the file system containing your mail directory is mounted:
~~~bash
# check if .mail is mounted
if [ ! -d ~/.mail ]; then
    echo ".mail does not seem to be mounted"
    exit
fi
~~~

## Desktop notification
You can use the [notifymuch](https://github.com/kspi/notifymuch) program to get desktop notifications of new mail. After installing it, you can call it at the end of the poll script:
~~~bash
notifymuch
~~~
When new mail arrives, `notifymuch` will display a notification. More details and configuration options can be found on their GitHub page.

Alternatively, you can use `wmctrl` to set the "URGENT" flag on the `astroid` window when new mail arrives:
~~~bash
NEW_MAIL=false
if ! (notmuch new | grep "No new mail."); then
    NEW_MAIL=true
fi

# Tag email etc. here

if [ $NEW_MAIL = true ]; then
    wmctrl -b add,demands_attention -r "Astroid"
fi
~~~
If your window manager supports it, this will bring your attention to the `astroid` window.

## Automatic tagging
Automatic tagging of mail can be achieved through `notmuch tag --batch`. See notmuch-tag(1) for details.

To target only incoming mail, we follow the approach in [Approaches to initial tagging of messages](https://notmuchmail.org/initial_tagging/).
First, add the following to `~/.notmuch-config`:
~~~
[new]
tags=new;
~~~
If this property already exists in your `notmuch` config, append `new;` to the list of tags that are added.

Now you can add the following to your poll script, after `notmuch new`:
~~~bash
notmuch tag --batch <<EOF

    # Tag urgent mail
    +urgent tag:new and subject:URGENT

    # Tag all mail from GitHub as "work"
    +work tag:new and from:github

    # We've finished processing incoming mail
    -new tag:new
EOF
~~~
**Note**: If you don't use `tag:new` in your queries, you may overwrite tags on old mail that you have set manually.

Random tips related to this:
- You can tag email arriving at different accounts using the `to:` query.
- You can tag mailing lists using the `lists:` query.
- You can tag email from certain people with the `from:` query.
- Threads can be muted using [excluded tags](https://notmuchmail.org/excluding/).

### Note: `notmuch` hooks
See [[Introduction to notmuch]] and notmuch-hooks(5) for an alternative place to put some of the above functionality.

## Scanning for viruses
You can scan attachments for viruses using a tool such as [`clamav`](http://www.clamav.net/). Alternatively, this can be done just before you open the attachment, as explained in [[Opening attachments and virus detection]].

## Advanced Polling

### Fast polling

With faster polling times it is possible to check for email more often and have it delivered more instantly. `IDLE` does not work very well in offlineimap, but it is possible to improve performance significantly. These tips are based on some [old offlineimap documentation](http://www.offlineimap.org/doc/versions/v6.5.6/MANUAL.html#synchronization-performance).

> Note that GMail seems to [throttle the IMAP connection](https://support.google.com/a/answer/1071518?hl=en), this makes could slow down synchronization of GMail accounts using offlineimap.

#### Quick sync
A quick sync only compares the number of files locally and on the IMAP side, this should detect changes in almost all cases (except if you delete an email remotely, and one is added so that the number is the same).

The following script demonstrates how to only do a full sync every two hours:

```sh
## do a full offlineimap sync once every two hours, otherwise only quicksync
lastfull_f=~/.cache/astroid/offlineimap-last-full # storing state
if [ -f $lastfull_f ]; then
  lastfull=$(cat $lastfull_f)
else
  lastfull=0
fi

delta=$((2 * 60 * 60)) # seconds between full sync
now=$(date +%s)
diff=$(($now - $lastfull))

if [ $diff -gt $delta ]; then
  echo "full offlineimap sync.."
  offlineimap -o || exit 1
  echo -n $now > $lastfull_f
else
  echo "quick offlineimap sync.."
  offlineimap -o -q || exit 1
fi

```

#### Settings that can increase speed
```
[general]
maxsyncaccounts = 2  # set to greater than the accounts you are syncing
socktimeout     = 10 # fail on slow network quicker than OS (I use this to fail quickly in case network fails / is turned off)
```

If you need to synchronize many folders or messages from the same account:
```
[repository RemoteA]
maxconnections = 3 # or something else
```

> Also: Use `folderfilter` to only synchronize necessary folders.

### External polling
By default astroid is designed to regularly initiate poll for new messages. If you have a set up which fetches e-mail outside astroid (e.g. a cron job, an IMAP IDLE watch, offlineimap running in the background) you need to notify astroid about new messages. You have two _alternatives_:

> You should **only use one of these alternatives** at the same time!

#### 1. Notify by indicating start and stop of polling
> This method enables astroid to figure out what changes have been made automatically, and show a polling spinner when the external polling is running

```sh
astroid --start-polling
# poll any changes
astroid --stop-polling
```

Make sure that you _always_ call `--stop-polling`, even if polling fails. Astroid will detect any changes between the two calls.

#### 2. Notify that anything since given database revision should be refreshed
> In this method you need to take care of what lastmod revision the notmuch database is before you start.

```sh
notmuch count --lastmod # parse and store database revision from before poll
# poll
astroid --refresh REVISION_BEFORE_POLL
```


# Instant email; from IMAP to the desktop
Here's a specific example of a complete email setup using Astroid as the MUA, to provide real-time collection and notification of messages in a modern Linux system (Ubuntu in this case), without relying on a particular desktop distribution.

Additionally, each component used can be replaced independently, as long as you keep an eye on the scripts and configs where some assumptions are encoded. As well as Astroid, I use `alot` to read my Maildir/notmuch occasionally. To refresh alot's UI, you send a SIGUSR1 signal. I have this in my notmuch `post-new` hook but it is not included in this example.

Email collection is handled by `fetchmail` using --IDLE to stay connected to the IMAP source folder (we can only use a single source folder in this case; if you need more, consider running multiple Fetchmail services). Fetchmail is controlled as a user-space systemd service, which gives us some of the flexibility we need for detailed management as we delve deeper. Fetchmail invokes `maildrop` as the Mail Delivery Agent, which creates any needed Maildirs (I deliver into folders named by the date, YYYYMM). Maildrop invokes `notmuch new` whenever it delivers a message, and notmuch's `pre-new` and `post-new` hooks signal Astroid and the desktop notification service as mail arrives. Astroid's `poll.sh` script is used to ensure that the Fetchmail service is running and knows how to contact Astroid (via the $DISPLAY environment variable).

~~~
* User login, PAM
 * systemd --user
  * fetchmail (does not have to start automatically)
   * maildrop
    * notmuch
     * pre-hook, post-hook
      * Notifications, Astroid
 * X Windows
  * Astroid
   * poll.sh
    * systemd --user restart fetchmail
~~~

### Fetchmail via systemd
Under most current Linux distributions, an instance of `systemd --user` is created by PAM's `/etc/pam.d/systemd-user` script. This will be able to consult system files in $HOME/.config/systemd/user/, and you can elect to enable them to run automatically if you want to, although in this case you don't need to.

~~~bash
$ cat ~/.config/systemd/user/fetchmail.service 
[Unit]
Description=Fetchmail from Exchange using IDLE

[Service]
# fetchmail gets all its config from the CLI (except for user password)
# maildrop uses $HOME/.mailfilter
# The password comes from either ~/.fetchmailrc or ~/.netrc - we're using ~/.netrc
# We're also using IMAP IDLE, which will keep the server connection open 'permanently'.
# Because of IDLE, we can only check a single folder, but that's OK.
# For maildrop, we must name the mailfilter file in order to avoid delivery mode,
# and therefore to be able to see the environment variable $DISPLAY (injected
# into systemd --user's session by astroid's poll.sh)
Type=simple
ExecStart=/usr/bin/fetchmail --syslog --mda "maildrop $HOME/.mailfilter" --all --nokeep --norewrite --username USERNAME --auth password --proto IMAP --ssl --idle --folder SOURCEFOLDER SERVER
Restart=always
RestartSec=5
~~~

### Maildrop stores items into Maildirs
Maildrop is a Local Mail Delivery Agent, similar in some ways to the venerable procmail. I'm using it to ensure that a Maildir named for the current YYYY and MM is present, then deliver the mail from fetchmail into it, then call `notmuch` to process the new arrival.

~~~bash
$ cat ~/.mailfilter
# maildrop - refile into YYYY/MM/ maildirs
logfile "/var/log/maildrop.log"
MDIR=$HOME/Mail

# File messages for the date that they were processed
# (allow the mail db and mua (notmuch, Astroid) to order them on presentation)
thisYYYYMM=`/bin/date +%Y%m`
`[ -d $MDIR/$thisYYYYMM ] || maildirmake $MDIR/$thisYYYYMM`
# Don't use 'to', because we want to run notmuch new *after* the file has been written
cc $MDIR/$thisYYYYMM/
`/usr/bin/notmuch new`
# Exit to avoid writing another copy of the message to $MAIL
exit
~~~

### Notmuch needs to signal to Astroid, if it can
Once `notmuch new` has been called, Maildrop's job is over. Notmuch is well-equipped with hooks to label messages, and in my case I'm using the default flag of 'new' only during the initial processing phase, it is removed as we finish.

This is where we have to be careful with $DISPLAY - if fetchmail started before the user had an X session established, $DISPLAY will not be present. I'm also using `logger` to put debug data into syslog, because this deep into the command invocation things can be very unclear!

~~~bash
$ cat .notmuch/hooks/pre-new 
#!/bin/dash

# notmuch pre-new hook ... messages have not yet been imported into the database

# Start the spinner on Astroid's UI
# It is a fatal error to not have DISPLAY. When Astroid starts, it should add
# DISPLAY to systemd --user's environment.
if [ "x$DISPLAY" != "x" ]
  then logger -t notmuch "Astroid polling start requested during pre-new hook"; /usr/local/bin/astroid --start-polling 2>&1 >/dev/null
fi

# Ensure that this script exits with success, otherwise notmuch will fail out.
exit 0
~~~

In the `post-new` hook, I do a fair amount of re-tagging. All messages being operated on by this command are tagged as 'new', and we're removing that tag at the end before the user gets them. Because I want the desktop notification popup to tell the user in the summary how many new messages were received (usually it's going to be "1" because we're using IMAP IDLE, but at the beginning of the session it'll be higher), I need to count the messages before we remove this tag. I've also chosen for the notification message to have some of the sender and subject line data, which I'm getting from another `notmuch` search for the 'new' tags, and then abusing JSON by parsing it with `sed`!

Once the 'new' tags are removed, I do the same as in the `pre-hook` to ensure we have $DISPLAY, then call notify-send to do the popup notifications (it isn't perfect, and you may prefer a different product), and finally Astroid `--stop-polling` to refresh the UI
~~~bash
$ cat .notmuch/hooks/post-new 
#!/bin/dash

# Use notmuch to apply more tags to matching messages that are new
# (inbound messages are tagged by ~/.notmuch.config as inbox;unread;new)

# Here I run a number of retagging commands, to recognise senders, subjects and so on
# notmuch tag +ADD_A_TAG -REMOVE_A_TAG -- tag:new and SEARCH_QUERY
...

# Prepare the desktop notification messages
newcount=$(notmuch count tag:new)
summary="NM: ${newcount} new message"
# Come on, who here doesn't actually hate "you have 1 new message(s)"?
if [ $newcount -gt 1 ]; then summary="${summary}s"; fi
if [ $newcount -gt 0 ]; then detail="$(notmuch search --output=summary --format=json tag:new | sed -e 's/.*authors": "//;s/|[^"]*"/"/;s/", "subject": "/ : /;s/".*//')"; fi

# Final rule, remove the 'new' tag once we're finished here
notmuch tag -new		-- tag:new

# See the notmuch pre-hook for DISPLAY thoughts
if [ "x$DISPLAY" != "x" ]
  then
    # Desktop notifications
    if [ $newcount -gt 0 ]; then logger -t notmuch "calling notify-send '$summary' '$detail'"; /usr/bin/notify-send -i /usr/share/icons/Humanity/actions/24/mail-message-new.svg "$summary" "$detail"; fi
    # Stop the astroid spinner; this will refresh the UI
    logger -t notmuch "Astroid polling stop requested during post-new hook" ; /usr/local/bin/astroid --stop-polling 2>&1 >/dev/null
fi

exit 0
~~~

### Astroid polls to make sure fetchmail is running
As a way to close the circle, Astroid's `poll.sh` script is used to export $DISPLAY into the `systemd --user` environment, and to restart the fetchmail service to make sure that it is propogated via the Fetchmail invocation itself. Fetchmail will die if the far end Exchange server resets the connection, which it will have to do from time to time, and while systemd will attempt to restart the service, eventually it will rack up too many restarts and fail. I've set `poll.interval` to 3600, which gives Astroid a reasonable chance to fix this problem.

~~~bash
$ cat ~/.config/astroid/poll.sh 
#!/bin/dash
# Polling script for astroid

# Ensure that DISPLAY is in the systemd environment
# Restart fetchmail service (to pick up any changes to DISPLAY)

echo "poll.sh running at $(date)"
/bin/systemctl --user set-environment DISPLAY="$DISPLAY"
/bin/systemctl --user restart fetchmail-from-exchange.service
~~~

### Normal logging from this setup
Here's an example of the normal syslog entries created by this process :- 
~~~
Mar 20 15:29:36 holdfast fetchmail[23663]: 1 message for USERNAME at SERVER (folder SOURCEFOLDER).
Mar 20 15:29:36 holdfast notmuch: Astroid polling start requested during pre-new hook
Mar 20 15:29:39 holdfast notmuch: calling notify-send 'NM: 1 new message' 'SENDER : SUBJECT'
Mar 20 15:29:39 holdfast org.freedesktop.Notifications[28641]: ** (notify-osd:29928): WARNING **: dnd_is_screensaver_active(): Got error "The name org.gnome.ScreenSaver was not provided by any .service files"
Mar 20 15:29:39 holdfast org.freedesktop.Notifications[28641]: ** (notify-osd:29928): WARNING **: dnd_is_idle_inhibited(): got error "The name org.gnome.SessionManager was not provided by any .service files"
Mar 20 15:29:39 holdfast notmuch: Astroid polling stop requested during post-new hook
Mar 20 15:29:39 holdfast fetchmail[23663]: reading message USERNAME@SERVER:1 of 1 (1667 header octets) (9465 body octets) flushed
~~~
There are two error messages created by `notify-send` that represent the fact I'm not actually running any desktop session that Gnome recognises. If you are, you probably won't see these errors being logged.

## Current unresolved problems with this approach
* If a remote X session is established (i.e. you use ssh to connect), and you then run a new instance of Astroid, you will probably get $DISPLAY updated correctly and be able to receive notifications on the remote session; however if a local copy of Astroid is still running, the two poll scripts will fight with each other as they run.
* Again for a remote session, you may well be able to talk to Astroid correctly, but if you require GPG support it is likely that your agent will still be running on the original $DISPLAY. This may make it effectively impossible to create messages until you kill the original agent (and therefore lose your session in there). When using `alot` (a console-based MUA similar to Astroid) I unset $DISPLAY, and that seems to encourage a text-based prompt from the GPG agent.
* Sometimes (most often when I send email out, apparently) it looks like `fetchmail` detects a new incoming message during `notmuch new`, and I get a Xapian lock conflict. Under these circumstances, the notmuch `post-hook` doesn't seem to run, and the Astroid poller doesn't stop.
~~~
Mar 20 11:24:06 holdfast sSMTP[23383]: Creating SSL connection to host
Mar 20 11:24:06 holdfast sSMTP[23383]: SSL connection using RSA_AES_256_CBC_SHA1
Mar 20 11:24:07 holdfast sSMTP[23383]: Sent mail for EMAILADDRESS (221 2.0.0 Service closing transmission channel) uid=UID username=USERNAME outbytes=2740
Mar 20 11:24:07 holdfast fetchmail[20530]: 1 message for USERNAME at SERVER (folder SOURCEFOLDER).
Mar 20 11:24:09 holdfast notmuch: Astroid polling start requested during pre-new hook
Mar 20 11:24:09 holdfast fetchmail[20530]: A Xapian exception occurred opening database: Unable to get write lock on MAILDIR/.notmuch/xapian: already locked
Mar 20 11:24:09 holdfast fetchmail[20530]: reading message USERNAME@SERVER:1 of 1 (1903 header octets) (1524 body octets) flushed
Mar 20 11:25:01 holdfast ... NEXT MESSAGE, i.e. no post-new invocation
~~~

