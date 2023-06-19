---
title: Features
bookHidden: true
# bookFlatSection: true
# bookToc: false
# bookCollapseSection: true
weight: 40
menu:
  before:
---

## Buttons

Buttons are styled links that can lead to local page or external link.

### Example

```tpl
{{</* button relref="/" [class="..."] */>}}Get Home{{</* /button */>}}
{{</* button href="https://github.com/alex-shpak/hugo-book" */>}}Contribute{{</* /button */>}}
```

{{< button relref="/" >}}Get Home{{< /button >}}
{{< button href="https://github.com/alex-shpak/hugo-book" >}}Contribute{{< /button >}}

## Columns

Columns help organize shorter pieces of content horizontally for readability.

```html
{{</* columns */>}} <!-- begin columns block -->
# Left Content
Lorem markdownum insigne...

<---> <!-- magic separator, between columns -->

# Mid Content
Lorem markdownum insigne...

<---> <!-- magic separator, between columns -->

# Right Content
Lorem markdownum insigne...
{{</* /columns */>}}
```

### Example

{{< columns >}}

### Left Content

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.

<--->

### Mid Content

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter!

<--->

### Right Content

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /columns >}}

## Font Stacks

### [Sans-serif](/notes/sans-serif.md)

```css
font-family: -apple-system, BlinkMacSystemFont, "Avenir Next", Avenir,
  "Nimbus Sans L", Roboto, Noto, "Segoe UI", Arial, Helvetica, "Helvetica Neue",
  sans-serif;
```

### [Serif](./notes/serif.md)

```css
font-family: Constantia, "Lucida Bright", Lucidabright, "Lucida Serif", Lucida,
  "DejaVu Serif", "Bitstream Vera Serif", "Liberation Serif", Georgia, serif;
```

## Details

Details shortcode is a helper for `details` html5 element. It is going to replace `expand` shortcode.

### Example

```tpl
{{</* details "Title" [open] */>}}
## Markdown content
Lorem markdownum insigne...
{{</* /details */>}}
```

```tpl
{{</* details title="Title" open=true */>}}
## Markdown content
Lorem markdownum insigne...
{{</* /details */>}}
```

{{< details "Title" open >}}

### Markdown content

Lorem markdownum insigne...
{{< /details >}}

## Callout Support

Amethyst supports the same Admonition-callout syntax as Obsidian.

See [documentation on supported types and syntax here](https://help.obsidian.md/How+to/Use+callouts#Types).

### Showcase

> [!EXAMPLE] Examples
>
> Aliases: example

> [!note] Notes
>
> Aliases: note

> [!abstract] Summaries
>
> Aliases: abstract, summary, tldr

> [!info] Info
>
> Aliases: info, todo

> [!tip] Hint
>
> Aliases: tip, hint, important

> [!success] Success
>
> Aliases: success, check, done

> [!question] Question
>
> Aliases: question, help, faq

> [!warning] Warning
>
> Aliases: warning, caution, attention

> [!failure] Failure
>
> Aliases: failure, fail, missing

> [!danger] Error
>
> Aliases: danger, error

> [!bug] Bug
>
> Aliases: bug

> [!quote] Quote
>
> Aliases: quote, cite

## Latex

Block math works with two dollar signs `$$...$$`

$$
f(x) = \int_{-\infty}^\infty
    f\hat(\xi),e^{2 \pi i \xi x}
    \,d\xi
$$

Inline math also works with single dollar signs `$...$`. For example, Euler's identity but inline: $e^{i\pi} = -1$

Aligned equations work quite well:

$$
\begin{aligned}
a &= b + c \\ &= e + f \\
\end{aligned}
$$

And matrices

$$
\begin{bmatrix}
1 & 2 & 3 \\
a & b & c
\end{bmatrix}
$$

## Mermaid

[MermaidJS](https://mermaid-js.github.io/) is library for generating svg charts and diagrams from text.

{{< hint info >}}
**Override Mermaid Initialization Config**

To override the [initialization config](https://mermaid-js.github.io/mermaid/#/Setup) for Mermaid,
create a `mermaid.json` file in your `assets` folder!
{{< /hint >}}

### Example

{{< columns >}}

```tpl
{{</*/* mermaid [class="text-center"]*/*/>}}
stateDiagram-v2
    State1: The state with a note
    note right of State1
        Important information! You can write
        notes.
    end note
    State1 --> State2
    note left of State2 : This is the note to the left.
{{</*/* /mermaid */*/>}}
```

<--->

{{< mermaid >}}
stateDiagram-v2
State1: The state with a note
note right of State1
Important information! You can write
notes.
end note
State1 --> State2
note left of State2 : This is the note to the left.
{{< /mermaid >}}

{{< /columns >}}

## Tabs

Tabs let you organize content by context, for example installation instructions for each supported platform.

```tpl
{{</* tabs "uniqueid" */>}}
{{</* tab "MacOS" */>}} # MacOS Content {{</* /tab */>}}
{{</* tab "Linux" */>}} # Linux Content {{</* /tab */>}}
{{</* tab "Windows" */>}} # Windows Content {{</* /tab */>}}
{{</* /tabs */>}}
```

### Example

{{< tabs "uniqueid" >}}
{{< tab "MacOS" >}}

## MacOS

This is tab **MacOS** content.

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /tab >}}

{{< tab "Linux" >}}

## Linux

This is tab **Linux** content.

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /tab >}}

{{< tab "Windows" >}}

## Windows

This is tab **Windows** content.

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /tab >}}
{{< /tabs >}}

## Page Configuration

You can specify additional params in the front matter of individual pages:

```toml
# Set type to 'docs' if you want to render page outside of configured section or if you render section other than 'docs'
type = 'docs'

# Set page weight to re-arrange items in file-tree menu (if BookMenuBundle not set)
weight = 10

# (Optional) Set to 'true' to mark page as flat section in file-tree menu (if BookMenuBundle not set)
bookFlatSection = false

# (Optional) Set to hide nested sections or pages at that level. Works only with file-tree menu mode
bookCollapseSection = true

# (Optional) Set true to hide page or section from side menu (if BookMenuBundle not set)
bookHidden = false

# (Optional) Set 'false' to hide ToC from page
bookToC = true

# (Optional) If you have enabled BookComments for the site, you can disable it for specific pages.
bookComments = true

# (Optional) Set to 'false' to exclude page from search index.
bookSearchExclude = true

# (Optional) Set explicit href attribute for this page in a menu (if BookMenuBundle not set)
bookHref = ''
```

### Partials

There are layout partials available for you to easily override components of the theme in `layouts/partials/`.

In addition to this, there are several empty partials you can override to easily add/inject code.

| Empty Partial                                      | Placement                                   |
| -------------------------------------------------- | ------------------------------------------- |
| `layouts/partials/docs/inject/head.html`           | Before closing `<head>` tag                 |
| `layouts/partials/docs/inject/body.html`           | Before closing `<body>` tag                 |
| `layouts/partials/docs/inject/footer.html`         | After page footer content                   |
| `layouts/partials/docs/inject/menu-before.html`    | At the beginning of `<nav>` menu block      |
| `layouts/partials/docs/inject/menu-after.html`     | At the end of `<nav>` menu block            |
| `layouts/partials/docs/inject/content-before.html` | Before page content                         |
| `layouts/partials/docs/inject/content-after.html`  | After page content                          |
| `layouts/partials/docs/inject/toc-before.html`     | At the beginning of table of contents block |
| `layouts/partials/docs/inject/toc-after.html`      | At the end of table of contents block       |

### Extra Customisation

| File                     | Description                                                                           |
| ------------------------ | ------------------------------------------------------------------------------------- |
| `static/favicon.png`     | Override default favicon                                                              |
| `assets/_custom.scss`    | Customise or override scss styles                                                     |
| `assets/_variables.scss` | Override default SCSS variables                                                       |
| `assets/_fonts.scss`     | Replace default font with custom fonts (e.g. local files or remote like google fonts) |
| `assets/_colors.scss`    | Change the default color schemes |
| `assets/mermaid.json`    | Replace Mermaid initialization config                                                 |

### Plugins

There are a few features implemented as plugable `scss` styles. Usually these are features that don't make it to the core but can still be useful.

| Plugin                            | Description                                                 |
| --------------------------------- | ----------------------------------------------------------- |
| `assets/plugins/_numbered.scss`   | Makes headings in markdown numbered, e.g. `1.1`, `1.2`      |
| `assets/plugins/_scrollbars.scss` | Overrides scrollbar styles to look similar across platforms |

To enable plugins, add `@import "plugins/{name}";` to `assets/_custom.scss` in your website root.

### Hugo Internal Templates

There are a few hugo templates inserted in `<head>`

- [Google Analytics](https://gohugo.io/templates/internal/#google-analytics)
- [Open Graph](https://gohugo.io/templates/internal/#open-graph)

To disable Open Graph inclusion you can create your own empty file `\layouts\_internal\opengraph.html`.
In fact almost empty not quite empty because an empty file looks like absent for HUGO. For example:
```
<!-- -->
```