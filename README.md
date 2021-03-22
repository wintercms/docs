# Docs

The documentation for Winter CMS, currently available at https://wintercms.com/docs/

To allow for automatic content updates and integration with GitHub Pages, the docs now use Jekyll to power the documentation.

### Set up a local copy

1. [Install Jekyll, Ruby and Bundler](https://jekyllrb.com/docs/installation/#requirements) on your operating system.
2. Clone the repository to your folder of choice. (`git clone git@github.com:wintercms/docs.git`).
3. Within the folder, run `bundle install` to install all dependencies for Jekyll and the docs.
4. Finally, run `bundle exec jekyll serve` to serve the docs locally. You can access the docs at [https://127.0.0.1:4000/docs/](https://127.0.0.1:4000/docs/)

### Writing docs

All documentation pages can be found in the `_docs` folder. Each documentation page is a Markdown document with the following metadata at the top of the page content:

```
---
title: (the title of the page)
navTitle: (the title used in navigation)
category: (the category - one of the following, "Setup", "CMS", "AJAX", "Themes", "Plugins", "Backend", "Database", "Services", "Console", "Extras")
order: (the order of the page, as a number, with lower numbers appearing first in the page list)
layout: default
---
```

This metadata controls the titling and ranking of each of the pages in the documentation. As we have one layout for the docs (currently), the layout option should always be `default`.

The categories list can be found in the `_data/categories.yaml` file, and controls the ordering of the categories in the nav.
