# Jekyll Static Site Generator

## Create a New Site

```bash
# Install Jekyll
gem install jekyll bundler

# Create a new site
jekyll new <site-name>

# Start a dev server to serve the site
cd <site-name>
bundle exec jekyll serve
```

The site should be available at http://127.0.0.1:4000/.

## Anatomy of a Site

Well-known folders/files:

| Folder        | Purpose                      |
| ------------- | ---------------------------- |
| /\_includes   | Partial templates            |
| /\_layouts    | Page templates               |
| /\_posts      | Individual pages in Markdown |
| /\_site       | Target for generated site    |
| /\_config.yml | Site configuration           |
| /index.md     | Site home page               |

## Front Matter

Each content and template file can include YAML at the top to define metadata (aka "front matter").

For example, front matter for a blog post might look like home page layout may be rendered inside a default base layout (\_includes/default.html):

```yaml
---
layout: gallery
title: Octopus
date: 2016-12-28 18:26:03 -0700
categories: photos
blurb: Red and Giant Pacific octopus found around the Puget Sound
image_base: /assets/site/photos/octopus
og_type: article
gallery_folder: octopus
photos:
  - 163b3580.jpg
  - 163b3584.jpg
  - 163b4107.jpg
  - 163b4657.jpg
  - 163b4725.jpg
---
```

## Creating Templates

### Base Template

It's a good idea to create a "base" template that defines the `<head>` and `<body>`.  For example, a very simple base template be as follows:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{{ page.title }} | {{ site.title }}</title>
    <link href="styles.css" rel="stylesheet">
</head>
<body>
    {% include header.html %}
    <div id="content">
        {{ content }}
    </div>
    {% include footer.html %}
</body>
</html>
```

Things to note:

- You can add conditional logic to add meta data, OG tags, page-specific assets, etc
- You can pull in external partial templates (e.g. header.html)
- Content is injected as the special `content` variable

### Page Templates

Create a separate template for each layout you want to support.  Usually you'll have a unique template for your home page (e.g. `_includes/home.html`):

```html
---  
layout: base  
---  

{% assign recentPosts = posts | slice:2,3 %}

<div class="home-page">
  (home page template)
</div>
```

### Partial Templates

You can create reusable bits of markup as "includes" that optionally accept parameters.  For example, a hero banner could be defined as:

```html
<div class="hero">
    <img class="hero-image" src="{{ include.image }}">
    <div class="hero-text">
        <h2>{{ include.text }}</h2>
    </div>
</div>
```

Then you could reference the include in a template:

```html
<div>
  {% include hero.html image="foo.jpg" text="bar" %}
</div>
```

## Creating Content

To do

## Themes

### File-Based Themes

The best approach is to fork/download the theme into your site root folder immediately after creating a new site.  The theme files basically become your site.

After installing the theme to your site, install any missing dependencies:

```bash
bundle install
```

### Gem-Based Themes

With gem-based themes, the theme files are stored separately in the gem and configured/overridden by your site files.

First, update your `Gemfile` to add the theme gem:

```yaml
gem "minima"
```

Then update your gems and view the structure so you know what files are available to be overridden:

```bash
gem update
open $(bundle show <theme name>)
```

## Building the Site

To do

## References

- [Jekyll Cheat Sheet](https://learn.cloudcannon.com/jekyll-cheat-sheet/)