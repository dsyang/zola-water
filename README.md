# water theme for Zola

A multi-lingual, lightweight and responsive theme built on water.css. It's intended to powerful yet simple, so if the theme gets in your way, or if something isn't clear, please consider it an issue and report it [on tildegit](https://tildegit.org/southerntofu/zola-water) or by email to the maintainer (MyUserName@thunix.net).

# Setup

Just download this repository and place it in your site's `themes/water` directory. Then, in your config.toml:

```
theme = "water"
```

If you would like to receive updates directly via git, you can clone the repository like this:

```
$ cd path/to/site/
$ mkdir themes # If it doesn't exist yet
$ git clone https://tildegit.org/southerntofu/zola-water themes/water
```

Then you can fetch updates, from the `zola-water` repository, with the `git pull` command. Please don't modify templates directly in the theme folder, as this would cause problems during updates. Instead, refer to the [Configuration](#configuration) and [Customization](#customization) sections.

## Updates

**This theme is a work-in-progress**. It's good enough for me and some other folks to use and cutomize for our needs, however some things will evolve over time to improve the overall experience. **Breaking changes** will be introduced, though never without reason.

The overall design of this theme takes careful evaluation, to avoid having to revert changes in the future.

# Configuration

If you want to simply configure the theme to match your needs, there's a few options at your disposal. If you want to replace parts of the templates, please head over to the [Customization](#customization) section of this page.

## Base template

There's a few options to configure the source for different parts of the base template. At the moment, only `header` and `menu` are configurable:

- `header` defines the source for the top of the page
- `menu` defines the source for the navigation menu, right under the `header` page

Each of these settings can be either the path to a page from the content folder, or "disabled". Setting a page requires a full path, including the `.md` extension, because Zola loads pages from either `page.md` or `page/index.md`. Disabling these parts with the "disabled" keyword.

**Note**: Pages defined in the config, like `header = _common/header.md` will be resolved according to the current language, so the french version of the site will call `_common/header.fr.md` automatically. This will provoke a crash when such a page is not translated in not all defined languages. (**TODO**: open a ticket on zola for support of `required` param in `get_page` function).

If you need more flexible customization of the templates, please refer to the [Customization](#customization) section on how to extend the theme's template from your site's templates folder.

**TODO**: In the future, every macro will have a corresponding shortcode, so that basic configuration of the layout can be achieved exclusively via Markdown files.

## Styles

### color

`color = "dark"` determines the default color scheme displayed to your users. This can be either `light` or `dark`, and is only applied to browsers who do not implement the [prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme) media query. More modern browsers will display the website as they please, and out of respect for your users, no simple way to change this is provided.

### stylesheets

`stylesheets = [ "style.css" ]` determines a list of additional stylesheets to load in the main template. By default, the theme provides a style.css stylesheet (generated from [this SASS file](https://tildegit.org/southerntofu/zola-water/src/branch/master/sass/style.scss)).

You can remove this stylesheet from the `stylesheets` variable and/or add stylesheets of yours:

- `stylesheets = []` disables any additional stylesheet
- `stylesheets = [ "style.css", "custom.css" ]` adds custom.css to the theme's stylesheets
- `stylesheets = [ "custom.css" ]` disable the theme's style.css and use custom.css instead

### max_width_for

`max_width_for = "main"` determines what part of the site has the it's CSS `max-width` property set. Upstream water.css applies it to the whole page, while some Zola sites may want to have a fullwidth header, for instance.

This setting can be "main" (default) or "body". To have the authentic water.css experience, set it to "body". The default value "main" applies it only to the main part of the page.

### max_width

`max_width = "800px"` determines the max-width applied to the `max_width_for` element. The default is 800px, as defined in water.css.

## Homepage

By default, the homepage (`content/_index.md`) is built as a landing page, taking the file's Markdown content into the main area of the website. However, the homepage can also be used to display a section's latest articles with the `home_section` setting. Here's an overview of the settings specific to the homepage.

### home_title

`home_title = true` can be true or false, configuring whether the section's title is displayed on the homepage. This applies to the homepage's title when `home_section` is not used(default), and to the home section's title otherwise.

### home_section

`home_section = ""` defaults to treating the home page as a simple page without children. Setting this variable to a section's name displays the latest articles of this section as the homepage.

Example: use `home_section = "blog"` to feature the blog/ section on the homepage

### home_fullarticles (when using home_section)

`home_fullarticles = false` decides whether to show article contents on the homepage. When disabled, articles will have their summary displayed, or simply their title/date when they don't have a summary. This option only applies to sites using a `home_section`.

# Customization

## Extending templates

Theme templates can be extended from your site templates. This means you can replace any "block" defined in the theme templates. The template path needs to be prepended with `water/templates/`. If your site's section.html wants to override the content block of the theme's section, it can do like this:

```
{% extends 'water/templates/section.html' %}
{% block content %}
<h2>My latest articles</h2>
<ul>
    {% for page in section.pages %}
    <li><a href="{{ page.permalink }}">{{ page.name }}</a></li>
    {% endfor %}
</ul>
{% endblock %}
```

## Replacing templates

You can replace a template by simply placing a template of the same name in your site's templates folder. If you replace a template that is extended by others (like index.html), make sure to define the same blocks as it does so children templates don't see the difference.

A template can both replace and extend itself. So that if you want to just change the title in index.html, all you have to do is create templates/index.html with:

```
{% extends 'water/templates/index.html' %}
{% block title %}MY CUSTOM TITLE{% endblock %}
```

**WARNING**: Replacing templates has limitations on vanilla Zola: if some other templates includes the template you replaced, the theme's version will be extended instead (replacing index.html is only supported on the fork)

# Taxonomies support

Zola has built-in support for [taxonomies](https://www.getzola.org/documentation/content/taxonomies/). These allow you to "tag" your content in a given category with multiple items of this category. For examples, your taxonomies can be:

```
taxonomies = [
  { name = "tags", rss = true },
  { name = "authors", rss = true },
]
```

In your content pages, you can then add in the frontmatter:

```
[taxonomies]
tags = [ "ssg", "tutorial" ]
authors = [ "foobar" ]
```

## Taxonomies templates

Then, you need to create templates for the taxonomies in your site's templates folder:

- tags/single.html <~~ about a specific tag
- tags/list.html <~~ tags list
- authors/single.html <~~ about a specific author
- authors/list.html <~~ authors list

The variables you can use in those templates are described on [zola docs](https://www.getzola.org/documentation/templates/taxonomies/). Please be aware that taxonomy templates have no title/content or related section/page variables.

**Note**: If your taxonomy template extends the theme's index.html, your site will crash if the template does not override the main block, as the one provided in index.html is intended for the homepage. This could be improved in the future, and patches are welcome!

**WARNING**: Taxonomies templates don't have a lang in Zola <= 10.1. If you are running Zola 10.1, please be sure to override the header block in your taxonomies templates or you will have a friendly error message displayed on your site (no crash).

**Note**: If you replace the theme's index.html entirely, please remember to include fallbacks for missing variables in taxonomies. These workarounds are marked "TAXONOMY" in the index.html so you can find them easily.

# Translations for multilingual sites (i18n)

In order to support translations, you need to enable languages in your site config. Then, you can translate markdown files by simply adding ".LANG" before the ".md" extension, where LANG is the language you want to translate it into. For example, `content/about.md` becomes `content/about.fr.md` in french. This is explained [in the multilingual docs](https://www.getzola.org/documentation/content/multilingual/) on Zola's website.

Simple translation strings to reuse across the templates are stored in the site's configuration file, as well as in the theme's `theme.toml`. They are described in a `extra.translations` section, which contains a subsection for each language, each containing a mapping of a key to a translated string. When a string is defined in both files, the one defined in the site config will be used.

**Note**: The strings are stored in the extra section, not in the `translations` field of the config, because these are not merged properly with the config translations, and the zola project has plans to deprecate these translation strings entirely in favor of [Fluent](https://projectfluent.org).

### Convenience macros

This theme includes a few macros to help deal with with translations. They are located in the `templates/widgets.html` file, which you simply need to import in your template, unless your template extends the theme's index.html (or any page extending it) which already imports it for you:

```
{% import "widgets.html" as widgets %}
```

#### t

Takes a `key` parameter. Returns the corresponding translation key, in the current language.

Example:

```
{{ widgets:t(key="readmore") }}
```

#### i18n_path

Takes a `path` parameter to a content page/section. Returns the path to the page in the current language.

Example:

```
<p>The current language's menu is in file {{ widgets::i18n_path(path="menu.md") }}</p>
```

#### i18n_content

Takes a `path` parameter to a content page (not section). Return the page's content in the current language.

Example:

```
<nav>{{ widgets::i18n_content(path="menu.md") }}</nav>
```

#### i18n_url

Takes a `path` parameter which is a URL relative to your site. Returns the requested URL, with the current language inserted between the base_url and the path (eg. "rss.xml" => "BASEURL/fr/rss.xml").

Example:

```
<link rel="alternate" type="application/rss+xml" href="{{ widgets::i18n_url(path="rss.xml") }}">
```

**Note**: this macro does not lookup translations, because these are not supported on all content (only pages and sections), but naively constructs the URL. Only use it for content you know share the same slug across languages. The base URL is also assumed to be the same across languages, because localized base URLs is not yet supported by zola.

### Translating taxonomies

In order to support translating names for taxonomies, you simply need to define those with an additional `lang` key in config.taxonomies:

```
taxonomies = [
  { name = "tags", lang = "en", rss = true },
  { name = "tags", lang = "fr", rss = true },
  { name = "authors", lang = "en", rss = true },
  { name = "auteurices", lang = "fr", rss = true },
]
```

Then, your translated pages can use the translated taxonomy names. Example page.fr.md:

```
[taxonomies]
tags = [ "ssg", "tutoriel" ]
auteurices = [ "foobar" ]
```

**Note**: I'm not sure if that's a feature or an anti-feature, for reasons explained here : https://zola.discourse.group/t/rfc-internationalization-system-rework/546/20 .

**TODO**: Implement/Document translations for display name and description of taxonomies.

# Template widgets

The theme features some nice, user-friendly content-organizing patterns such as described in [this article](https://staticadventures.netlib.re/blog/multi-column-layout/).

For the moment, only a menu widget is provided, but more are coming!

## menu widget

This widget comes in handy when you need to build a list of link, that you can further style with CSS. It takes a `content` argument and divides it according to the thematic breaks, furthermore stripping paragraph tags added during Markdown processing.

**Note**: this macro relies on ugly HTML hacks that do not work with every content, i hope in the future zola allows us to access the raw markdown content and maybe even exposes a filter to split a page by thematic breaks.

Example:

```
{{ widgets::menu(path="_common/menu.md") }}
```

Sample menu (can be translated):

```
+++
+++

[Home](@/_index.md)

===

[About](@/about.md)

===

[Contact](@/contact.md)
```

# For the future

## Current limitations

This theme is already quite cool, but there's a few limitations i would like to tackle:

- if you configure a header/menu = "foo.md", it needs to be translated in every language otherwise the site will crash; if you have ideas how to fix this without a [has_page](https://zola.discourse.group/t/rfc-i18n/13/28) template function, let me know!
- if you configure a header/menu, it cannot be the index page because of some weird reason i have no idea about
- pagination cannot work on the index page, because index.html doesn't know about the home section's paginator (only the home's paginator) ; maybe we could count the home section's children and auto append a link to SECTION/page/2 when it's more than X?
- we need more template blocks, for more customizability, so you don't have to edit huge chunks of templates just to modify a single value

## Future plans

- ensure really good accessibility
- test right-to-left languages
- provide some interesting shortcodes
- support other plug-and-play CSS stylesheets than water.css
- support using only relative URLs so the same site output can be used across different baseURLs
- add some cool, useful templates:
  - FAQ
  - "about me"
  - a contributing guide page produced by the theme
  - alternative names page to discover how to access the same page over different browsers/protocols
  - test results page, to integrate with a CI
  - build log page, to integrate with a CD
