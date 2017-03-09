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
  offlineimap -u quiet || exit 1
  echo -n $now > $lastfull_f
else
  echo "quick offlineimap sync.."
  offlineimap -q -u quiet || exit 1
fi

```

#### Settings that can increase speed
```
[general]
maxsyncaccounts = 2 # set to greater than the accounts you are syncing
```

If you need to synchronize many folders or messages from the same account:
```
[repository RemoteA]
maxconnections = 3 # or something else
```

> Also: Use `folderfilter` to only synchronize necessary folders.

