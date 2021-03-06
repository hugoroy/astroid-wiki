Astroid is only a "Mail User Agent" and so it won't fetch or sync your email for you. Astroid will "poll" your mail, i.e. it will call other programs that take care of fetching, syncing or indexing your new mail (for example, see [our introduction to notmuch](./Introduction-to-notmuch) and see [offlineimap](http://offlineimap.org/)).

You can poll your mail by pressing `P` (by default, bound to `main_window.poll`) which will execute the script found at `~/.config/astroid/poll.sh`. An example script can be found in the [examples](https://github.com/gauteh/astroid/tree/master/examples) directory; if all you need is `offlineimap` and `notmuch new` you can copy this example script to the right location and make it executable. 

Examples of more advanced functionality and how to achieve fast poll times can be found in [[Advanced Polling and Processing mail]].

Automatic polling is controlled by `poll.interval`, and is specified in seconds (e.g. 160). To disable, set `poll.interval` to some value `<= 0`.