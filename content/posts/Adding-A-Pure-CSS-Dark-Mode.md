+++
date = '2026-02-03T12:17:41-05:00'
draft = false
tags = ["meta", "code"]
title = 'Adding a Pure CSS Dark Mode'
+++

My eyes are only a little less displeased with bright white sites than they are with direct sunlight. To support my own preferences, and to flatter myself with the idea that someone else will visit this page, I'll demonstrate how I added a CSS-Only dark and light mode to this theme using the `light-dark()` function.

## CSS light-dark()
`light-dark()` is broadly supported in modern browsers. It allows two different colors to be declared for the same CSS color property within the parentheses of the function, separated by a comma. Presumably, the developer places the light value first, and the dark value second. Then, the browser renders either the light OR the dark values based on the preferences on the user's machine. Example from MDN docs [^1]:
```CSS
:root {
  /* This property is required */
  color-scheme: light dark;
}
body {
  /* The light-dark function can now do its work */
  color: light-dark(#333b3c, #efefec);
  background-color: light-dark(#efedea, #223a2c);
}
```


## Shibui CSS Quirks
All of the code in this section comes directly from the defaults in the `main.css` file of the theme.

Shibui uses "knobs" that each represent a value in an overall HSL(hue, saturation, lightness) color value:
``` CSS
  /* Base theme knobs */
  --bg-h: 0;
  --bg-s: 0%;
  --bg-l: 99%;
```

Each of these knob values gets plugged in to base color variables, which contain calculations that assure the theme remains aesthetically consistent even as you "turn" the knobs by changing the HSL values. These base color variables are what get used directly in the CSS color properties. The knob values do not ever interact directly with anything other than the base color variables.
``` CSS
  /* Background colors */
  --color-bg-primary: hsl(var(--bg-h) var(--bg-s) var(--bg-l));
  --color-bg-secondary: hsl(var(--bg-h) var(--bg-s) calc(var(--bg-l) - 2%));
  --color-border: hsl(var(--bg-h) var(--bg-s) calc(var(--bg-l) - 6%));
  --color-selection-bg: hsl(var(--bg-h) var(--bg-s) calc(var(--bg-l) - 13%));

  /* Text colors with WCAG-friendly contrast */
  /* Primary: ensures near-black on light bg and near-white on dark bg */
  --color-text-primary: hsl(var(--bg-h) var(--bg-s) clamp(8%, calc(100% - var(--bg-l)), 92%));

  /* Muted: softer but still >4.5:1 contrast */
  --color-text-muted: hsl(var(--bg-h) var(--bg-s) clamp(30%, calc(85% - var(--bg-l)), 75%));

  /* Code text: slightly brighter than muted */
  --color-text-code: hsl(var(--bg-h) var(--bg-s) clamp(20%, calc(90% - var(--bg-l)), 85%));
```

It's a *really* convenient way to implement theme colors and one of the things I adore about this theme.

## Adding a CSS-Only Light and Dark Mode to Shibui
For simplicity's sake, you may just copy-paste this snippet into your `custom.css`. This code creates CSS-Only, automatic light and dark modes by implementing a separate set of hsl "knobs" for dark mode. Simply change the light knob values to tweak the light mode, and change the dark knob values to tweak the dark mode.

**Before setting and forgetting, note that this theme's CSS can conflict with syntax highlighting styles for code blocks. It's easily noticeable when you have a theme that switches from light to dark mode dynamically. I have workarounds for this issue in this snippet and explain more in sections below.**

```SCSS
root {
/* Adds a light and dark mode based on system preferences */
  color-scheme: light dark;

  /* Theme knobs for light mode */
  --bg-h: 0;
  --bg-s: 0%;
  --bg-l: 99%;

  /* Theme knobs for dark mode */
  --bg-h-dark: 0;
  --bg-s-dark: 0%;
  --bg-l-dark: 12%;


  /* Background colors */
  --color-bg-primary: light-dark(hsl(var(--bg-h) var(--bg-s) var(--bg-l)), hsl(var(--bg-h-dark) var(--bg-s-dark) var(--bg-l-dark)));

  --color-bg-secondary: light-dark(hsl(var(--bg-h) var(--bg-s) calc(var(--bg-l) - 2%)), hsl(var(--bg-h-dark) var(--bg-s-dark) calc(var(--bg-l-dark) - 2%)));

  --color-border: light-dark(hsl(var(--bg-h) var(--bg-s) calc(var(--bg-l) - 6%)), hsl(var(--bg-h-dark) var(--bg-s-dark) calc(var(--bg-l-dark) - 6%)));

  --color-selection-bg: light-dark(hsl(var(--bg-h) var(--bg-s) calc(var(--bg-l) - 13%)), hsl(var(--bg-h-dark) var(--bg-s-dark) calc(var(--bg-l-dark) - 13%)));


  /* Text colors with WCAG-friendly contrast */
  /* Primary: ensures near-black on light bg and near-white on dark bg */
  --color-text-primary: light-dark(hsl(var(--bg-h) var(--bg-s) clamp(8%, calc(100% - var(--bg-l)), 92%)), hsl(var(--bg-h-dark) var(--bg-s-dark) clamp(8%, calc(100% - var(--bg-l-dark)), 92%)));

  /* Muted: softer but still >4.5:1 contrast */
  --color-text-muted: light-dark(hsl(var(--bg-h) var(--bg-s) clamp(30%, calc(85% - var(--bg-l)), 75%)), hsl(var(--bg-h-dark) var(--bg-s-dark) clamp(30%, calc(85% - var(--bg-l-dark)), 75%)));

  /* Code text: slightly brighter than muted */
  --color-text-code: light-dark(hsl(var(--bg-h) var(--bg-s) clamp(20%, calc(90% - var(--bg-l)), 85%)), hsl(var(--bg-h-dark) var(--bg-s-dark) clamp(20%, calc(90% - var(--bg-l-dark)), 85%)));


  /* Addresses an issue that causes syntax highlighting to inherit
    values from main.css in a way that hinders text visibility
    */
  .highlight *, pre code *{
    /* Purges the problematic color variable from syntax highlighting 
    */
    color: unset;
  }

}

```


## Fixing Code Highlighting
There is a conflict in the base CSS of this theme that makes some of the text in syntax-highlighted code blocks difficult to read depending on the theme's background color. This conflict is caused by line 113 in `main.css`. Here is its block:
```CSS
* {
  margin: 0;
  padding: 0;
  font: inherit;
  color: var(--color-text-primary); /* Problem line */
  box-sizing: border-box;
}
```
Syntax highlighting styles[^2] set in the `hugo.toml` config file are strangely inheriting the color property from this wildcard selector. 

Using a light hue for the theme background will generate a near-black `--color-text-primary`. Using a dark hue for the theme background will generate a near-white `--color-text-primary`. This is a desirable and intentional feature, but it has an undesired side-effect due syntax-highlighting styles inheriting from the wildcard selector.

If you have a light theme background and choose a dark syntax highlighting style: Text that inherits `--color-text-primary` is near-black and camouflages on the dark code blocks.

If you have a dark theme background and choose a light syntax highlighting style: Text that inherits `--color-text-primary` is near-white and camouflages on the light code blocks.

 The effect is not exclusive to a light mode/dark mode implementation, but it is more noticeable in one.

The code I provided earlier should fix this, but it's important to note that these syntax-highlighting styles don't shift dynamically with light and dark modes out of the box. Below, I have a few approaches that could be taken if you wanted code blocks to shift dynamically with light and dark modes.

### Simple
Set codeFences to false: `codeFences = false` in `hugo.toml`.

```TOML
[markup]
  # ... other properties...
    [markup.highlight]
      codeFences = false # Disables syntax highlighting
      guessSyntax = true
      lineNos = false
      tabWidth = 4
      style = "witchhazel"

```

This will *disable* syntax highlighting. The styling of code blocks will no longer depend on the language's syntax and the style property. Instead, code blocks will only take on the styles in the theme's CSS. This is easy and allows the code blocks to take on the dynamic light/dark color switching, but it takes some readability out of the code blocks.




### Less Simple: Customizing the CSS Highlight Class
Hugo allows you to customize syntax highlight styles with CSS files it generates for them.
You would need to set `noClasses = false` in `hugo.toml`, and then generate the CSS files with hugo commands:
`hugo gen chromastyles --style=catppuccin-frappe > static/css/syntax.css`

Here are some resources for this:
https://gohugo.io/content-management/syntax-highlighting/

https://www.justinmklam.com/posts/2025/05/hugo-tips-and-tricks/#adaptive-syntax-highlighting

It's more work, but it could allow syntax highlighting that changes dynamically with dark and light mode.
I haven't tried to do this with the `light-dark()` function, so I don't have specific guidance on this method. It also requires commitment to a chosen syntax highlight style, so any copy-paste solution is potentially nonviable because people have varying preferences for the style.


<br>


[^1]: MDN light-dark() reference -> https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/color_value/light-dark
[^2]: List of syntax highlighting styles you can choose in the config file -> https://gohugo.io/quick-reference/syntax-highlighting-styles/#styles





