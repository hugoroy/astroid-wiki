Astroid needs some information to start with. You can setup Astroid, provided you already have notmuch working (if not, see [our introduction to notmuch][notmuchintro]).

***

If you have not compiled and installed Astroid yet, refer to: [[Compiling and Installing]].

***

# Configuration

Astroid uses the `$XDG_CONFIG_HOME/astroid` directory (or `$HOME/.config/astroid` if it is not set) for storing its configuration file. When you first run astroid it will set up the default configuration file there. This is a JSON file created by [boost::property_tree]. Options not set here will be set to their default values as specified in [`src/config.cc`](https://github.com/gauteh/astroid/blob/master/src/config.cc#L78).

[boost::property_tree]: http://www.boost.org/doc/libs/1_56_0/doc/html/property_tree.html


By default astroid looks in `$HOME/.mail` for the notmuch database, but you can change this in the configuration file. You can also set up default queries and accounts for sending e-mail there.

you can run:

` $ astroid --new-config `

to create a new configuration file in the default location, you can also specify a location of the new config file with the `-c` argument.


## Where to find your mail database

You should probably look at the following properties specifically to see if they match your setup:

`"astroid.notmuch.db": "~/Mail"` should point out to the directory where your mail is. This is the same as the `database.path` for [notmuch][notmuchintro].

## What your email account is

Some self-explanatory variables that you probably want to configure are (where `exampleaccount` is an arbitrary identifier for your email account):

```json
"accounts": {
  "exampleaccount": {
    "name": "Jane Doe",
    "email": "jane.doe@example.org",
    "gpgkey": "0x000000000",
    "save_sent_to": "/home/jane/Mail/sent/cur/",
    "save_drafts_to": "/home/jane/Mail/drafts/"
  }
},
```

## How to send email

At last, you need to define which program to call to send your email. For instance:

* if you use [msmtp](http://msmtp.sourceforge.net/): `"accounts.id.sendmail": "msmtp --read-envelope-from -t"` 
* if you use a sendmail compatible solution (e.g. postfix), the line should be: `"sendmail": "sendmail -t"`

[notmuchintro]: ./Introduction-to-notmuch

------------------

Next Step: [Polling your email](./Polling)