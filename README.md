# yet.org

Welcome to the repository for [yet.org](https://yet.org), the personal website of Sébastien Braun.

## About the project

*yet.org* was created in 2006 by *Sébastien Braun*, a technology and innovation enthusiast. The site originally ran on *[Movable Type](http://www.movabletype.org/)* and focused primarily on science fiction and related ideas. It later evolved into a *MediaWiki* [encyclopedia](http://wiki.yet.org), and in late 2012 a new iteration began with a fully static website powered by *[nanoc](http://nanoc.stoneship.org/)*.

The original choice of nanoc was driven by its flexibility: it is not just a blogging platform, but a general-purpose toolkit for building custom digital experiences. After more than a decade of running the site, the project has now moved to a lighter dependency stack based on *[Hugo](https://gohugo.io/)*.

This repository contains both the source for the website and the content published on the site, including articles written in *MultiMarkdown*.

The name *YET* stands for *Yet Emerging Technologies*.

## What is in this repository?

This project is a static site built with Hugo and includes:

- content and posts in `content/`
- site templates and page layouts in `layouts/`
- static assets in `static/`
- theme configuration and theme files in `themes/`
- front matter templates in `archetypes/`
- site configuration in `hugo.yaml`

## Local development

To run the site locally, make sure you have Hugo installed and then use:

```bash
hugo server
```

This will start the local development server so you can preview the site in a browser.

## Licensing

[<img alt="Apache 2.0 License" src="http://www.apache.org/images/feather-small.gif" width="100"/>](http://www.apache.org/licenses/LICENSE-2.0.html)

All posts published on this website and the associated site code are licensed under the _Apache 2.0 License_.

## Credits

Special thanks to the following people and projects, which made this website possible:

- The team behind [Hugo](https://gohugo.io/)
- nanxiaobei, the designer of the [HugoPaper](https://github.com/nanxiaobei/hugo-paper) theme

## Sharing and reuse

This project is shared publicly so others may learn from, reuse, or adapt parts of the work where appropriate. The content and code are meant to be useful, and feedback is welcome.
