+++
date = '2026-02-03T12:17:41-05:00'
draft = false
tags = ["meta", "code"]
title = 'Adding a Pure CSS Dark Mode'
+++

As a fan of dark mode, I can say my eyes are only a little less displeased with inescapably bright white sites than they are with direct sunlight. I have also known enough people who rely on dark modes for accessibility to know that it's more than just a question of preference. However, light mode is also a legitimate accessibility tool, as people with astigmatism have reported discomfort with dark mode. Thus, they are equally important to support.

## CSS light-dark()
To support my own preferences, and to flatter myself with the idea that someone else will visit this page, I'll demonstrate how I added a CSS-Only dark and light mode to this theme using the CSS `light-dark()` function.

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

## How-to
Adding light-dark() to Shibui was incredibly simple and only a little ugly.

Dear reader, for simplicity's sake, you may just copy-paste this snippet into `custom.css` in the theme's files. This code creates CSS-Only, automatic light and dark modes by implementing a separate set of hsl "knobs" for dark mode. Simply change the light knob values to tweak the light mode, and change the dark knob values to tweak the dark mode.

**Before setting and forgetting, note that adding a dark mode like this has a conflict with some of the theme's CSS. This shows up in syntax highlighting which is responsible for changing the colors on code blocks formatted with triple ticks. There will be more on that below.**

```CSS
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
}

```

 If anyone desires to know, these are the steps I took within the `custom.css` theme file:
1. Add the required `color-scheme` property.
2. Create a secondary set of "knobs" for dark mode with `-dark` appended to each variable name.
3. Add the `light-dark()` function to each color variable that derives directly from the knobs. They're all in one place, so it's easy.
4. Use the original hsl calculated value as the first arg of `light-dark()`.
5. As the second arg for `light-dark()`, use a copy of the hsl calculated values with each knob variable replaced by its respective dark knob variable.

## Fixing Code Highlighting With Dark Mode
There is a conflict in the base CSS of this theme that makes some of the text in syntax-highlighted code blocks difficult to read when you implement a dark and light mode together. This conflict is caused by line 113 in `main.css`. Here is its block:
```CSS
* {
  margin: 0;
  padding: 0;
  font: inherit;
  color: var(--color-text-primary); /* Problem line */
  box-sizing: border-box;
}
```
The `color` attribute in this block causes most out-of-the-box syntax highlighting styles to incorporate `--color-text-primary`. These styles are static by default, and they are not inherently compatible with dynamic light/dark modes. Since they happen to incorporate the color attribute given to the wildcard, they create an idiosyncratic flaw. While using a highlight style with a dark background, a code block may camouflage dark text against its dark background when light mode is active. Whereas, while using a highlight style with a light background with dark mode active, a code block may camouflage light text against its light background. I have a few approaches to mitigate this problem.

### Highlight Styles
The simplest approaches both involve tweaking configuration properties in the `[markup.highlight]` section in the `hugo.toml` file of the base site (*not* the theme's config file). Here's an example of the section that will be adjusted in the next two approaches:

```TOML
[markup]

  #...other markup properties

    [markup.highlight]
      codeFences = true
      guessSyntax = true
      lineNos = false
      noClasses = true
      style = "catppuccin-mocha"
      tabWidth = 4

  #...other markup properties
```

#### Simplest
Set codeFences to false: `codeFences = false`.

This will *disable* syntax highlighting, so the styling of code will no longer depend on the language's syntax and the style attribute. Instead, code blocks will only take on the styles in the theme's CSS. While easy, this takes some readability out of code blocks.

#### Simple
 Try to find a style that doesn't depend on the base font color. I personally think it's a little dumb that any of them do this, but almost all of them do. At the time I wrote this post, it seemed that the rose pine styles (rose-pine, rose-pine-dawn, and rose-pine-moon) did not do this, so that's what I will use in the example:  `style = "rose-pine-moon"`.
 The downside of this is that you will have the single theme for all code block highlighting, irrespective of light or dark mode preference.

  ```TOML
    [markup.highlight]
      codeFences = true
      guessSyntax = true
      lineNos = false
      noClasses = true
      style = "rose-pine-moon" # <- This style seems to work
      tabWidth = 4
```

 Here is a link to a list of styles Hugo supports -> https://gohugo.io/quick-reference/syntax-highlighting-styles/#styles

#### Less Simple
Comment out the `color: var(--color-text-primary);` in line 113 in the wildcard block of `main.css` and instead place that attribute in a body selector block. Also, create a block for the `hr` and `a`
elements and put the same attribute inside of it because they all lose their color styling otherwise.
For example:
```SCSS
/* in main.css */
* {
  margin: 0;
  padding: 0;
  font: inherit;
  /*color: var(--color-text-primary); <- Comment this out */
  box-sizing: border-box;
}

...

/* in custom.css */
:root {
  /* ...Other styles... */

  /* Define color properties alternatively */
  body {
    color: var(--color-text-primary);
  }

  a {
    color: var(--color-text-primary);
  }

  hr {
    color: var(--color-text-primary);
  }

}

```

Now, any syntax highlighting style should have full text visibility. Keep in mind, the syntax highlighting set in the config file *will not* respect the changes between light and dark mode.


#### Customizing the CSS Highlight Class - Most Complex
Hugo allows you to customize syntax highlight styles with CSS files it generates for them.
You would need to set `noClasses = false` in `hugo.toml`, and then generate the CSS files with hugo commands:
`hugo gen chromastyles --style=catppuccin-frappe > static/css/syntax.css`

Here are some resources for this:
https://gohugo.io/content-management/syntax-highlighting/

https://www.justinmklam.com/posts/2025/05/hugo-tips-and-tricks/#adaptive-syntax-highlighting

It's more work, but it could allow syntax highlighting that changes dynamically with dark and light mode.
I haven't tried to do this with the `light-dark()` function, so I don't have specific guidance on this method. It also requires commitment to a chosen syntax highlight style, so any copy-paste solution is potentially nonviable because people have varying preferences for the style.

<br>


[^1]: MDN light-dark() page -> https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/color_value/light-dark





