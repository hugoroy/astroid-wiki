The [[poll.sh|Polling]] script can contain various functionality for, e.g., processing incoming mail. Here are some examples of that.

## Checking internet connectivity
You can check for internet connectivity before fetching mail. This may prevent programs from hanging (?). At the top of the poll script:
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
Automatic tagging of mail can be achieved through `notmuch tag --batch`. See notmuch-tag(1) for details. Here is an example, which should be run after `notmuch new`:
~~~bash
notmuch tag --batch <<EOF

    # Tag urgent mail
    +urgent subject:URGENT

    # Tag all mail from GitHub as "work"
    +work from:github

EOF
~~~
**Note**: This is applied to all mail, not only incoming mail, so it may overwrite tags on old mail that you have set manually. See [this page](https://notmuchmail.org/initial_tagging/) for a way to target only new mail.

Random tips related to this:
- You can tag email arriving at different accounts using the `to:` query.
- You can tag mailing lists using the `lists:` query.
- You can tag email from certain people with the `from:` query.
- Threads can be muted using [excluded tags](https://notmuchmail.org/excluding/).

## Scanning for viruses
You can scan attachments for viruses using a tool such as [`clamav`](http://www.clamav.net/). Alternatively, this can be done just before you open the attachment, as explained in [[Opening attachments and virus detection]].