<a href="https://raw.githubusercontent.com/gauteh/astroid/master/doc/astroid-editor-vim.png">
    <img src="https://raw.githubusercontent.com/gauteh/astroid/master/doc/astroid-editor-vim.png">
  </a> <a href="https://raw.githubusercontent.com/gauteh/astroid/master/doc/astroid-compose-code-highlight.png">
    <img src="https://raw.githubusercontent.com/gauteh/astroid/master/doc/astroid-compose-code-highlight.png">
  </a>

To change the editor, check out [[Customizing editor]].

### Quote string when replying or forwarding

The quote strings can be customized in the configuration options: `mail.reply.quote_line` and `mail.forward.quote_line`. Both of these take two optional arguments `%1`, the sender, and `%2`, a formatted date.

The quote string is passed through [`Glib::DateTime::format()`](https://developer.gnome.org/glibmm/stable/classGlib_1_1DateTime.html#a820ed73fbf469a24f86f417540873339) so you remove `%2` and specify your own date format if you want. Note that the leader in the `..::format()` documentation is the usual `%`, not `\` as is specified there.