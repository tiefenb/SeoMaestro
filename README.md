# Seo Maestro

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Build Status](https://travis-ci.org/wanze/SeoMaestro.svg?branch=master)](https://travis-ci.org/wanze/SeoMaestro)

A ProcessWire module helping you to manage SEO related tasks like a boss! 😎✌️

* Automatically generates and maintains a sitemap from your pages.
* Includes a Fieldtype and Inputfield to manage sitemap settings and meta data for pages (title, description, opengraph, twitter etc.).
* Configure default values for meta data on template level and let pages inherit or overwrite them individually.
* Map existing fields to meta data, reducing the need to duplicate content.

## Requirements

* ProcessWire `3.0` or newer
* PHP `7.0` or newer

## Installation

Install the module from the [modules directory]() or with Composer:

```
composer require wanze/processwire-seomaestro:^0.1 --no-dev
```

## Configuration

The _Seo Maestro_ module offers the following configuration:

* **`Enable sitemap generation`** Automatically generates and maintains a sitemap file.
* **`Sitemap path`** Path and filename of the sitemap relative from the ProcessWire root directory.
* **`Cache time`** A time in minutes how long the sitemap should be cached.
* **`Base url`** The base url used for all page links in the sitemap, e.g. `https://yourdomain.com`. If empty, the current domain is used.
* **`Default language`** 2-letter language code of the default language, needed to ensure a correct sitemap for
multilanguage sites.

> Change `sitemap.seomaestro.xml` to `sitemap.xml` once you checked that the sitemap file is correct.
The default name reduces the risk to accidentally overwrite an already existing file.

### Configure Meta Data and Sitemap Settings for Pages

The meta data and the sitemap configuration of each page is managed with the included Fieldtype.
Go ahead and create a new field of type *Seo Maestro*, a good name for the field is `seo` 😄. 

* Configure default meta data under _Details_. For text based metatags, you may enter strings or placeholders to 
map existing fields. For example, if your template contains a `lead_text` field which should be used for the 
`description` meta tag by default, use the placeholder `{lead_text}`. It is also possible to combine strings and placeholders. 
The following example appends the company name after a page's title: `{title} | acme.com`.
* The opengraph image tag supports placeholders as well: By referencing an image field holding multiple images, the first
one is used. For example, `{images}` would pick the first image from the `images` field. 
* Each page inherits meta tag values and sitemap configuration by default, but may override them individually.
* Under the _Input_ tab, configure which meta data is displayed to the content editor when editing pages.

> ℹ️ Edit the field in the context of a template to override any of the default data per template.

### Sitemap Generation

If enabled, the module hooks after `ProcessWire::finished` to generate the sitemap after the request is finished.

* The sitemap is only generated if the current user is logged in and the current page is an admin page.
* It only includes pages of templates having a _Seo Maestro_ field, in order to read the sitemap settings.
* It includes hidden pages.
* It excludes pages not viewable for the guest user.

### Output Meta Tags

Meta tags must be rendered in the `<head>` region of your templates:

```php
// Render all meta tags.
echo $page->seo->render();

// Render only the opengraph tags.
echo $page->seo->opengraph->render();
```

## API

The module offers an _easy-to-use_ API to modify meta data and sitemap configuration for pages:

```php
$page->of(false);

// Set values as strings or placeholders or combine both.
$page->seo->meta->title = '{title}';
$page->seo->opengraph->description = 'A description for opengraph';

// Inherit the Twitter card value from the field configuration.
$page->seo->twitter->card = 'inherit';

// Include the page in the sitemap and bump its priority.
$page->seo->sitemap->include = 1;
$page->seo->sitemap->priority = 0.9;

$page->save();
```

Values are always set for the current language. Switch the language to set values in a different language:

```php
$current = $user->language;

$user->language = $languages->get('de');

$page->of(false);
$page->seo->opengraph->title = 'Hallo Welt';
$page->save();

$user->language = $current;
```
