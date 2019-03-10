# Vanilo E-commerce How-to Articles

To read these files in a nice way, go to: https://vanilo.io/how-to/

This repository contains the source markdown files and images
for the Vanilo How-to Articles.

## Contributing

Feel free to contribute by submitting new articles via pull requests.

**Acceptance Criteria** for Submitted Articles:

- Topic must to be relevant to Vanilo.
- The article must be in the root folder of the repo.
- The article must be in valid markdown format with `.md` file extension.
- The filename must be in `kebab-case` (ie. all lowercase letters, no underscore, hyphens for word separator, eg.: `using-custom-theme-for-the-admin-panel.md`).
- The filename shouldn't start with `how-to` (it's already on the path).
- File assets must be in a separate folder matching the filename of the article, eg.: `using-custom-theme-for-the-admin-panel/screenshot1.png`.
- The article uses proper English grammar.
- The file must begin with a [YAML front matter](https://jekyllrb.com/docs/front-matter/) section containing at least the following data:
  - `title`": the title of the article in [Title Case](https://en.wikipedia.org/wiki/Capitalization#Title_case).
  - `excerpt`: Short (1-2 sentences) summary of the article. Gets shown in the article list.
  - `date`: The date of the article in `YYYY-mm-dd` format, eg.: `2019-04-21`.
- The article must not begin with the title/h1, because it gets added from the title from the yaml front matter section.

### Example Article

~~~markdown
---
title: Using Custom Theme for the Admin Panel
excerpt: This article guides you through the steps of using a custom theme for the Vanilo (AppShell) Admin Panel
date: 2019-03-11 
---
In case you want to customize the admin panel of Vanilo, these are the steps you have to do this and that.

Use some intro text, and use short code examples to illustrate:

```php
//in app/Providers/AppServiceProvider.php:
public function boot($app)
{
    // This is BS, but this is a demo-demo article, ok? ;)
    $app->appShell->theme->setPrimaryColor('#4349dd');
}
\```

> Use Markdown features like blockquotes.

## Use Headings To Partition The Article

It helps people to understand your article better.

### Use Multiple Heading Levels

Avoid using H1 since that's the title of the article, start from H2 for the major sections of your
article.

Also don't over-articulate, using more than 2-3 levels is usually unnecessary.

Have fun!
~~~
