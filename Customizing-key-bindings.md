You can customise keybindings in the `~/.config/astroid/keybindings` file.

For instance by default, by pressing the `L` key, you'll notice it's bound to `main_window.search_tag`. Redefining this binding to a new key (e.g. `/`) is possible with adding this in the keybindings file:

```
main_window.search_tag=/ 
```

> You can get a list of all keybindings by running the [`devel/get_keys.py`](https://github.com/gauteh/astroid/blob/master/devel/get_keys.py) script. You can use the stdout from this script as a template for your own `keybindings` file: `$ devel/get_keys.py > keybindings`. Remember to only keep the lines that you actually change.


See [here](https://github.com/aliceriot/dotfiles/blob/master/astroid/keybindings) for an example keybindings file that tries to emulate Sup and Vimium (c for compose, J/K for tab switching, etc).

> Empty lines and lines starting with `#` will be ignored.

### Unbound targets

There are also unbound targets (actions that do not have keybinding by default) that you can bind to keys, these are for instance:

```
# thread view
thread_view.forward_inline=f
thread_view.forward_attached=M-f

# thread index
thread_index.forward_inline=f
thread_index.forward_attached=M-f
```

this example will overwrite the default keybinding `f` which forwards a message `inlined` or `attached` depending on the configuration option `mail.forward.disposition`. In this particular example the configuration option no longer has any effect, and you choose whether to inline or attach a message depending on the keybinding.

Read on to find out how to run custom scripts on keypress and integrate them with Astroid: [[User defined keyboard hooks]].

## Available Bindings
The key bound can be any combination of the modifiers 'C-' (control), and 'M-' (Meta) followed by either a single unicode character (e.g. '?', 'F', 'a') or a GDK keysym as defined in [gdkkeysyms.h](https://git.gnome.org/browse/gtk+/plain/gdk/gdkkeysyms.h) without the GDK_KEY_ prefix. Note that this is case sensitive. So 'BackSpace' is a valid key to bind, but 'backspace', and 'GDK_KEY_BackSpace' are not.

### Unbinding
To unbind keys put nothing after the '=' sign, for instance
```
thread_view.search_tag=
```
would bind that to nothing, making searching-by-tag an inaccessible action from within astroid