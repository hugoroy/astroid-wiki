## Thread view

> The thread-view is using Webkit to render the messages, the style and layout can be customized by editing the HTML and SCSS files.

You can copy the standard HTML and SCSS file from the [ui/ directory](https://github.com/gauteh/astroid/tree/master/ui) to `~/.config/astroid/ui/` and then customize them to your own needs. Astroid will check if the version is correct (do not change it yourself), in case a new version is released upstream.

> SCSS works much like CSS, but it is preprocessed at startup using [libsass](http://sass-lang.com/libsass).

You can change `font-size`, `font-family` and some other variables for the whole style by just setting the SCSS variables at the top of [thread-view.scss](https://github.com/gauteh/astroid/blob/master/ui/thread-view.scss).

##### Only change variable settings - and use the rest of the default styles
If you only want to change the standard variables, but still use the default theme and _follow_ any updates made upstream, create a `~/.config/astroid/ui/thread-view.scss` file, set the variables, and then include the standard file:

> Remember to keep the version line at the top intact!

```css
/* ui-version: 3 (do not change when modifying theme for yourself) */
/* Variables */
/* Background colors associated with received emails: */
$recv-normal: #ffffff;
$recv-quoted: #e8e8e8;
$recv-collapsed: #f5f5f5;
/* Background colors associated with sent emails: */
$sent-normal: #ffffff;
$sent-quoted: #e8e8e8;
$sent-collapsed: #f5f5f5;
/* Fonts */
$font-base-size: 13px;
$font-mono: "Input Mono", "Inconsolata", monospace;
$font-sans: "Input Sans", "Roboto", sans-serif;
$font-family-default: $font-mono;
/* Colours */
$email-solid: #d8d8d8;
$focused-solid: #4a90d9;

@import '/path/to/upstream/astroid/ui/thread-view.scss';
```

## Thread index

Check the configuration options in `~/.config/astroid/config`

### Dates

You can customize the date format in Thread Index with three settings in your config file.

`general.time.clock_format` takes the options `24h` (default), `12h`, `local` and `year`. Using `year` you can avoid dates like "Yesterday" or "2h ago". Using `local` it uses your computer's default locale date format.

With `general.time.same_year` and `general.time.diff_year` you can configure the look of the dates for the current year and for all other years. It takes variables like the ones from GNU/Linux's `date` command. For example `%d.%m. %H:%M` will look like `29.08. 18:50` on August 29 at 18:50 in the current year. Using `%x` may be well suited for different years, since it looks like `29.08.2015`.

Please be aware that the look of dates depend on your computer's locale, especially when it comes to the 24/12h clock format or the placement of days, months and years for sequences like `%x`.