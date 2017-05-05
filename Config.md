
All default config options should be found in the source code of astroid: [config.cc](https://github.com/astroidmail/astroid/tree/master/src/config.cc).

# astroid.notmuch_config 

This should point to your [notmuch] configuration file path.

[notmuch]: http://notmuchmail.org/

## Example

	
	{
		"astroid": {
			"notmuch_config": "\/home\/alice\/.notmuch-config"
			}
	}



# astroid.hints.level

Astroid's interface will display hints on several occasions to guide the user through certain actions (e.g. how to edit a draft email or how to cancel an email being sent). These hints are assigned a level.

This option enables you to filter out some hints by level. The higher you set this option, the less hints will be displayed.

By default, it is `0`, i.e. only hints for which Astroid has assigned a level above 0 will be displayed.

## Example

To ensure that all hints are displayed, set this to `-1`.

	{
		"astroid": {
			"hints": {
				"level": "-1"
			}
	}


# astroid.notmuch options

These options should be set initially by parsing the [notmuch] [config file](#astroid.notmuch_config).

# astroid.notmuch.db

This should probably point to the path where you have your Maildirs.

## Example

	{
		"astroid": {
			"notmuch": {
				"db": "~\/Mail"
			}
	}

# astroid.notmuch.exclude_tags

These are the notmuch tags used to [exclude certain tagged message from notmuch queries results](https://notmuchmail.org/excluding/).

## Example

	{
		"astroid": {
			"notmuch": {
				"excluded_tags": "muted,spam,deleted,ignore,junk"
			}
	}


# astroid.notmuch.sent_tags

## Example

	{
		"astroid": {
			"notmuch": {
				"sent_tags": "sent"
			}
	}

# thread_index.cell.hidden_tags

Astroid will display tags attached to messages in a thread. Some tags are [“special” because created notmuch automatically attaches them](https://notmuchmail.org/special-tags/). You may want to hide some of these tags from Astroid.

## Example

	{
		"thread_index": {
			"cell": {
				"hidden_tags": "attachment,flagged,unread,encrypted,signed,replied,passed,sent,lists"
			}
		}
	}
