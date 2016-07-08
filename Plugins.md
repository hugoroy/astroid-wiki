Check out [astroid-plugins/official-examples](https://github.com/astroidmail/astroid-plugins), for instance: `ThreadViewExamplePlugin/tagformat.py` and the method `format_tags`. Put this folder in `.config/astroid/plugins/` and run astroid through the wrapper script: `examples/astroid` (to make sure `GI_TYPELIB_PATH` is set when you do not install astroid).

Corresponding issue: #156.

Documentation:

## General

The environment variable `ASTROID_CONFIG` will be set by astroid, it points to the configuration file usually located in: `~/.config/astroid/config`.

## ThreadViewActivatable

##### Objects
`thread_view`: [GtkBox](https://developer.gnome.org/gtk3/stable/GtkBox.html) where the main TreeView is a child.
`web_view`: A [WebKitWebView](http://webkitgtk.org/reference/webkitgtk/stable/webkitgtk-webkitwebview.html)

##### Functions
`string uri = get_avatar_uri (string email, GMime.Message message)`
> make sure the uri (or the first part of it) is returned in `get_allowed_uris()`

`[list of strings] allowed_uris = get_allowed_uris ()`

`string html_markup = format_tags (string background_color, [list of strings] tags, bool selected)`
> See documentation for `ThreadIndex::format_tags plugin`.

## ThreadIndexActivatable

##### Objects
`thread_index` : [GtkBox](https://developer.gnome.org/gtk3/stable/GtkBox.html) where the main TreeView is a child.

##### Functions
`string pango_markup = format_tags (string background_color, [list of strings] tags, bool selected)`
> This can be used to format tags just like you want: #139.
> background_color: (e.g. #ffffff) the color of the background the tag is rendered on (typically used for calculating luminosity when using alpha values)