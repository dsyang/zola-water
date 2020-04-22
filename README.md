# water theme for Zola

A multi-lingual, lightweight and responsive theme built on water.css. It's intended to powerful yet simple, so if the theme gets in your way, or if something isn't clear, please consider it an issue and report it [on tildegit](https://tildegit.org/southerntofu/zola-water) or by email to the maintainer (MyUserName@thunix.net).

**WARNING**: Some features do not work on the official (vanilla) Zola. These specific features will be marked with an additional warning.
TODO: link to southerntofu's fork while features are considered upstream.

# Setup

Just download this repository and place it in your site's `themes/water` directory. Then, in your config.toml:

```
theme = "water"
```

Please don't modify templates directly in the theme folder, as this would cause problems during updates. Instead, refer to the [Configuration](#configuration) and [Customization](#customization) sections.

**WARNING**: ([PR](https://github.com/getzola/zola/pull/997)) On vanilla zola, you also need to copy the translation strings to your config.toml :

```
[translations.fr]
source = "Source de cette page"
readmore = "Lire la suite"
previousPage = "Page précédente"
nextPage = "Page suivante"
dateFormat = "%d/%m/%Y"
[translations.en]
source = "Source for this page"
readmore = "Read more"
previousPage = "Previous page"
nextPage = "Next page"
dateFormat = "%m/%d/%Y"
```

## Updates

This theme is a work-in-progress so there's plenty of updates all the time. As it is considered alpha stage, breaking changes are not listed and may occur at any time, though never without good reason.

If you want to get updates semi-automatically, fetch the theme using git:

```
$ mkdir themes
$ git clone https://tildegit.org/southerntofu/zola-water themes/water
```

Then, to fetch updates:

```
$ cd themes/water
$ git pull
```

# Configuration

If you want to simply configure the theme to match your needs, there's a few options at your disposal. If you want to replace parts of the templates, please head over to the [Customization](#customization) section of this README.

**WARNING**: ([PR](https://github.com/getzola/zola/pull/997)) Vanilla zola does not support overriding a simple key from config.extra subsection, such as config.extra.styles. For the moment, we only use 1-level keys (home.title -> home_title) for this reason, but this will change in the future!

## Base template

There's a few options to configure the source for different parts of the base template. At the moment, only `header` and `menu` are configurable:

- `header` defines the source for the top of the page
- `menu` defines the source for the navigation menu, right under the `header` page

Each of these settings can be either the path to a page from the content folder, or "disabled". Setting a page requires a full path, including the `.md` extension, because Zola loads pages from either `page.md` or `page/index.md`. Disabling these parts with the "disabled" keyword.

If you need more flexible customization of the templates, please refer to the [Customization](#customization) section on how to extend the theme's template from your site's templates folder.

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

By default, the homepage (content/_index.md) is built as a landing page, taking the file's Markdown content into the main area of the website. However, the homepage can also be used to display a section's latest articles with the `home_section` setting. Here's an overview of the settings specific to the homepage.

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

# FAQ

## How to support taxonomies

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

Then, you need to create templates for the taxonomies in your templates folder:

- tags/single.html <~~ about a specific tag
- tags/list.html <~~ tags list
- authors/single.html <~~ about a specific author
- authors/list.html <~~ authors list

The variables you can use in those templates are described on [zola docs](https://www.getzola.org/documentation/templates/taxonomies/).

## How to support translations

TODO short links to zola docs about how to deal with translations in most cases

### Convenience macros

This theme includes a few macros to help deal with with i18n concerns. They are located in the widgets.html file, which you simply need to import in your template, unless your template extends the theme's index.html which already imports it for you:

```
{% import "widgets.html" as widgets %}
```

In most templates, the `lang` variable is set. However that's not the case in all templates.
TODO: figure out where that was not the case

#### i18n_path

Takes a `path` parameter to a content page/section, and a `lang` parameter. Returns the path to the page in the requested language.

Example:

```
<p>The current language's menu is in file {{ widgets::i18n_path(path="menu.md", lang=lang) }}</p>
```

#### i18n_content

Takes a `path` parameter to a content page (not section), and a `lang` parameter. Retuns the page's content in the requested language.

Example:

```
<nav>{{ widgets::i18n_content(path="menu.md", lang=lang) }}</nav>
```

#### i18n_url

Takes a `path` parameter which is a URL relative to your site, and a `lang` parameter. Returns the requested URL, with the language inserted between the base_url and the path (eg. "rss.xml" => "BASEURL/fr/rss.xml").

Example:

```
<link rel="alternate" type="application/rss+xml" href="{{ widgets::i18n_url(path="rss.xml") }}">
```

**Note**: this macro does not lookup translations, but naively constructs the URL. Only use it for content you know share the same slug across languages.

### i18n taxonomies

In order to support local names for taxonomies, you simply need to define those with an additional `lang` key in config.taxonomies:

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

# Other niceties

TODO

# Current limitations

TODO

- on zola stable, having a template that rewrites a theme template WILL break stuff, you should use extends isntead though that's less powerful
- if you do replace index.html with your own, it needs to support the same blocks as the theme's index.html otherwise other pages will break
- if you include a header = "foo.md", it needs to be translated in every language otherwise the site will crash (should be allowed by i18n refinements in the future) -> use ln -s header.md header.fr.md to keep them synced
- the pages you include for header/menu CANNOT be "_index.md" for some weird reason (special case for homepage??)
- homepage as section cannot paginate, because index.html would have the paginator of the index section, not of the target section... this is a limitation of zola not the theme itself
