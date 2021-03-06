You can choose whether to use an embedded editor (default, but does not work on Wayland) or external editor.

## Embedded editor

The default configured editor is `gvim` embedded using [XEmbed](https://specifications.freedesktop.org/xembed-spec/xembed-spec-latest.html), but any editor or setup that support `XEmbed` can in principle be used.

The `editor.cmd` string is passed to `ustring::compose`, and the following arguments must be set:
> _filename_: `%1`  
> _servername_: `%2` (probably optional)  
> _socketid_: `%3`

It is important that the editor is run in the _forground_ since the process is watched by astroid.

> The embedded editor does not work on Wayland, use the external editor there.

## External editor

To use an external editor, set the option `editor.external_editor` to `true`, and specify only the file argument (`%1`) in the editor command:

```
"editor": {
        "cmd": "gvim -f -c 'set ft=mail' '+set fileencoding=utf-8' '+set ff=unix' '+set enc=utf-8' %1",
        "external_editor": "true",
    },
```


## Editor examples

### gvim (default)
```sh
gvim -geom 10x10 --servername %2 --socketid %3 -f -c 'set ft=mail' '+set fileencoding=utf-8' '+set ff=unix' '+set enc=utf-8' %1
```

fancy `gvim` with [Goyo](https://github.com/junegunn/goyo.vim) and [Limelight](https://github.com/junegunn/limelight.vim):

**Embedded**: `gvim -geom 10x10 --servername %2 --socketid %3 -f -c 'set ft=mail' '+set fileencoding=utf-8' '+set enc=utf-8' '+set ff=unix' -c Goyo -c Limelight! %1`  
**External**: `gvim -f -c 'set ft=mail' '+set fileencoding=utf-8' '+set enc=utf-8' '+set ff=unix' -c Goyo -c Limelight! %1`

<img src="https://raw.githubusercontent.com/gauteh/astroid/master/doc/astroid-editor-vim.png" width="70%"/>

### emacs
**Embedded**: `emacs --parent-id %3 %1`  
**External**: `emacs %1`

<img src="https://raw.githubusercontent.com/gauteh/astroid/master/doc/astroid-editor-emacs.png" width="70%"/>

#### Pro-tip: use emacsclient
**External**: `emacsclient -q -c %1`

Make sure you have the daemon running or specify a suitable `-a` argument.

#### Pro-tip: use notmuch-message-mode

You can use the notmuch-message-mode for composing email in emacs by having this in your emacs config file (replace `user@localhost.domain.tld` with your username and machine hostname):

```el
(require 'notmuch)
(add-to-list 'auto-mode-alist '("user@localhost.domain.tld" . notmuch-message-mode))
```

### neovim (through [`st`](http://st.suckless.org/))
```sh
st -f 'Monospace' -w %3 -e nvim %1
``` 

you could use another terminal, but it must support `XEmbed`, like [st](http://st.suckless.org/).

## Message ID

You can [configure astroid](./Astroid-setup#configuration) to use a specific username and hostname for the message-id:

```json
{
    "mail": {
        "message_id_fqdn": "localhost.domain.tld",
        "message_id_user": "user"
    }
}
```