---
layout: post
title: Versioned Documentation with Jekyll
category: Jekyll
tags: [Jekyll, GitHub, Open-Source, Documentation]
author: Nicholas Bering
---

This summer I was doing some work on Angular Google Chart, and ran into a little issue. The project's original creator had built it so that all work was done on the gh-pages branch. This meant that if I pushed a breaking change, or a bug, it would break the documentation site.

So, I restructured the git repo to use a git-flow module, with a separate documentation branch, with it's own isolated history, served with Github-Pages.

## Enter Dr. Jekyll

Jekyll is a static site builder, written in ruby. If the gh-pages branch of your Github Repository is a Jekyll project, when you push updates GitHub will automatically build the site with Jekyll and serve the built site, rather than the raw content.

Jekyll supports processing a number of text markup languages to HTML, as well as syntax highlighting and a few other cool features.

Jekyll has it's complications, but I went with Jekyll for the Angular Google Chart documentation because it helped handle all the page layout rendering without having to build the docs site as an SPA.

## Atomic Research

I was looking for a way to write versioned docs with Jekyll, and stumbled across the documention to Electron, the application shell for the Atom editor. They are using a Jekyll collection to hold the API documentation, with a folder for each version.

https://github.com/atom/electron.atom.io

The folder structure ends up looking something like this:

```bash
.
├── _config.yml
├── _docs/
│   ├── 0.0.11/
│   │   ├── README.md
│   │   ├── directives/
│   │   │   └── googleChart.md
│   │   ├── directives.md
│   │   ├── examples/
│   │   │   ├── annotation.html
│   │   │   ├── annotation.js
│   │   │   ├── bar.html
│   │   │   ├── bar.js
│   │   │   ├── column.html
│   │   │   ├── column.js
│   │   │   ├── gauge.html
│   │   │   ├── gauge.js
│   │   │   ├── hide-series.html
│   │   │   ├── hide-series.js
│   │   │   ├── multi-chart.html
│   │   │   ├── multi.js
│   │   │   ├── pie.html
│   │   │   └── pie.js
│   │   ├── examples.md
│   │   ├── guides/
│   │   │   └── getting-started.md
│   │   ├── guides.md
│   │   ├── providers/
│   │   │   └── googleJsapiUrl.md
│   │   ├── providers.md
│   │   ├── services/
│   │   │   └── googleChartApiPromise.md
│   │   ├── services.md
│   │   ├── values/
│   │   │   └── googleChartApiConfig.md
│   │   └── values.md
│   ├── 0.1.0/
│   │   ├── README.md
│   │   ├── directives/
│   │   │   ├── agcBeforeDraw.md
│   │   │   ├── agcOnError.html
│   │   │   ├── agcOnMouseout.md
│   │   │   ├── agcOnMouseover.md
│   │   │   ├── agcOnReady.md
│   │   │   ├── agcOnSelect.html
│   │   │   └── googleChart.md
│   │   ├── directives.md
│   │   ├── examples/
│   │   │   ├── annotation.html
│   │   │   ├── annotation.js
│   │   │   ├── bar.html
│   │   │   ├── bar.js
│   │   │   ├── column.html
│   │   │   ├── column.js
│   │   │   ├── gauge.html
│   │   │   ├── gauge.js
│   │   │   ├── hide-series.html
│   │   │   ├── hide-series.js
│   │   │   ├── multi-chart.html
│   │   │   ├── multi.js
│   │   │   ├── pie.html
│   │   │   └── pie.js
│   │   ├── examples.md
│   │   ├── guides/
│   │   │   └── getting-started.md
│   │   ├── guides.md
│   │   ├── providers/
│   │   │   └── googleJsapiUrl.md
│   │   ├── providers.md
│   │   ├── services/
│   │   │   └── googleChartApiPromise.md
│   │   ├── services.md
│   │   ├── values/
│   │   │   └── googleChartApiConfig.md
│   │   └── values.md
│   └── latest/
│       ├── README.md
│       ├── directives/
│       │   ├── agcBeforeDraw.md
│       │   ├── agcOnError.html
│       │   ├── agcOnMouseout.md
│       │   ├── agcOnMouseover.md
│       │   ├── agcOnReady.md
│       │   ├── agcOnSelect.html
│       │   └── googleChart.md
│       ├── directives.md
│       ├── examples/
│       │   ├── annotation.html
│       │   ├── annotation.js
│       │   ├── bar.html
│       │   ├── bar.js
│       │   ├── column.html
│       │   ├── column.js
│       │   ├── gauge.html
│       │   ├── gauge.js
│       │   ├── hide-series.html
│       │   ├── hide-series.js
│       │   ├── multi-chart.html
│       │   ├── multi.js
│       │   ├── pie.html
│       │   └── pie.js
│       ├── guides/
│       │   └── getting-started.md
│       ├── guides.md
│       ├── providers/
│       │   └── googleJsapiUrl.md
│       ├── providers.md
│       ├── services/
│       │   └── googleChartApiPromise.md
│       ├── services.md
│       ├── values/
│       │   └── googleChartApiConfig.md
│       └── values.md
├── _includes/
├── _layouts/
├── _posts/
├── css/
├── examples.md
├── index.html
└── versions.md
```

Ok. This is a bit much to show (and I removed a couple versions to simplify the view). I thought I'd leave it verbose like this to demonstrate the one downside. With this approach, we're keeping every version of the documentation in the repository at the same time. But that's kind of the point. We want all versions of documentation to be on the website at the same time. If that's not your goal, I wouldn't recommend this setup. It does make more maintenance work in the long-run.

### Config

Now that we see how the folder structure works, let's take a look at how to set things up in the files. First, we'll need to add some things to `_config.yml`.

```yaml
latest_version: 0.1.0
available_versions:
    - 0.1.0
    - 0.1.0-beta.2
    - 0.1.0-beta.1
    - 0.0.11

collections:
    docs:
        output: true
        permalink: "docs/:path/"
```

`latest_version` and `available_versions` refer to the versions of the documentation. These values are for us to generate contents pages and `latest_version` helps to generate links to the correct version for version-neutral pieces of the site.

The `collections` section is related to the collections feature of Jekyll. `docs` becomes the name of our collection, `output: true` means the documents will be rendered to the site as files. There is another way to work with collections where the entries just exist as data to use in liquid templates. We want files. `permalink: "docs/:path/"` tells jekyll how to structure the URL for our collection pages.

### Collection Folder

By convention, since our collection is named `docs`, it will go into the `_docs` folder. Then we add a folder for each version, plus as folder called `latest` that holds a duplicate of the docs for the current version, with a small change to the frontmatter for each file.

```bash
.
└── _docs/
    ├── 0.0.11/
    ├── 0.1.0/
    └── latest/
```

### Version Contents Pages

Each version folder has a contents page like this one, located at `./_docs/0.1.0/README.md`:

```
{% raw %}---
layout: docs
title: Documentation
version: 0.1.0
latest: false
permalink: /docs/0.1.0/index.html
---

- [Guides]({{site.baseurl}}/docs/{{page.version}}/guides/)
{% assign guides = site.docs | where:'version', page.version | where:'category', 'Guides' | where:'latest', page.latest %}
{% for guide in guides %}
{% if guide.title != guide.category %}
  - [{{ guide.title }}]({{ guide.url | prepend: site.baseurl }})
{% endif%}
{% endfor %}

- [Examples]({{site.baseurl}}/docs/{{page.version}}/examples/)
{% assign examples = site.docs | where:'version', page.version | where:'category', 'Examples' | where:'latest', page.latest %}
{% for example in examples %}
{% if example.title != example.category %}
  - [{{ example.title }}]({{ example.url | prepend: site.baseurl }})
{% endif%}
{% endfor %}

...

{% endraw %}
```

There's a few more sections repeating like that for that sections for each category. I think I can make this a bit cleaner, but that's an evening project for another day.

### Versions List

To make a version list available, `./versions.md` is added.

```
{% raw %}---
layout: default
permalink: /docs/
title: Docs
---
# Versions

{% for version in site.available_versions %}
##### [{{ version }}]({{ site.baseurl l}}/docs/{{ version }}/)
{% endfor %}
{% endraw %}
```

Because this file is a plain page, with yaml frontmatter in the project root (not in a collection), Jekyll pics it up and adds it to the site menu, if we're using something like the default site layout. This is exactly what I wanted, which is why it's title is "Docs", and not "Versions."

## Isolated Examples

Jekyll's YAML headers came in real handy for setting up the interactive examples pages. I added some meta-data to the examples pages that reference the JavaScript files. The page layout adds the script tags to the page with liquid templates.

Combined with Cdnjs.com hosting of all the project versions, the layout adds the correct version of the Angular Google Chart library from the CDN for the project version by the version key in the frontmatter. Also, by not using Angular's routing as the project's original documentation had, broken code in one example page will not affect the rest of the site. And different versions of AngularJS and Angular Google Chart can be used in different parts of the site.

### Link to the Latest

Copy of the files in the base folder so that the link appears in the menu.
