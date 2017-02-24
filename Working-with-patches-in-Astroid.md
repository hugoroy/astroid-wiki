## Applying a single patch
1. Open the message
1. Press `Y` to yank the raw source of the message to the primary clipboard
1. Open a terminal (or the drop-down terminal using `|`)
1. Navigate to the repository
1. Apply the patch using [`git am`](https://git-scm.com/docs/git-am) (or typically `git am -3`)
1. Press `Shift-Insert` to paste the patch into `stdin`
1. Press `Ctrl-D` to complete the paste

## Applying a series of patches
1. Open the thread
1. Mark the messages containing patches using `t` (or `T` to mark all)
1. Press `;` to apply an action to all marked messages
1. Press `Y` to create an `mbox` with a synthesis of the raw marked messages, this is stored on the clipboard
1. Open a terminal (or the drop-down terminal using `|`)
1. Navigate to the repository
1. Apply the patch using [`git am`](https://git-scm.com/docs/git-am) (or typically `git am -3`)
1. Press `Shift-Insert` to paste the patch into `stdin`
1. Press `Ctrl-D` to complete the paste

## Saving a series of patches for later merging
1. Open the thread
1. Mark the messages containing patches using `t` (or `T` to mark all)
1. Press `;` to apply an action to all marked messages
1. Press `s` to save the marked messages individually to a desired directory
1. Apply the patches using [`git am`](https://git-scm.com/docs/git-am)

## Applying an inline diff
1. Open the message
1. Press `y` to yank the decoded message content to the primary clipboard
1. Open a terminal (or the drop-down terminal using `|`)
1. Navigate to the repository
1. Apply the patch using [`git apply`](https://git-scm.com/docs/git-apply) and `Shift-Insert` to paste the diff into `stdin`
1. Press `Ctrl-D` to complete the paste

## Sending a revised series using [git-send-email](https://git-scm.com/docs/git-send-email)
1. Open the original series or the e-mail you wish to reply to
1. Press `Ctrl-y` to yank the message-ID of the original message (this is now stored on the primary clipboard)
1. Paste this to the `--in-reply-to=` command line option for [`git-send-email`](https://git-scm.com/docs/git-send-email) using `Shift-Insert` in your terminal.