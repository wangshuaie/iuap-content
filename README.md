# iUAP技术文档投稿指南

你找到的这个GitHub库发布到iUAP文件中心在线技术文档的源 [http://iuap.yonyou.com](http://iuap.yonyou.com)文件。

该库还包含指导来帮助你贡献给我们的技术文件。

## 投稿到iUAP的文档

感谢您对蔚蓝的文档感兴趣！

* [贡献的方式]（贡献#方式）
* [你]蔚蓝的内容贡献（#你蔚蓝的贡献内容的）
* [组织]（#库库的组织）
* [使用GitHub，git，这个库]（#使用GitHub的Git和这个库）
* [如何使用Markdown格式主题]（#如何使用Markdown格式你的话题）
* [反馈，评论，和支持]（投稿指南/反馈意见和评论）
* [资源]（#更多资源）
* [索引的所有贡献者指南文章]（投稿指南/投稿指南索引）（打开新页面）


## 贡献的方式

你可以借助于iUAP的文件在几个不同的方式:

* 贡献可以到[论坛讨论](http://iuap.yonyou.com/blog/).
* 在文章的底部提交评论.
* 你可以很容易的在GitHub的用户界面技术文章投稿。要么找到该知识库中的文章，或访问[http://iuap.yonyou.com](http://iuap.yonyou.com)和点击的文章中的文章GitHub源链接.
* 如果你是一个现有的文章作出实质性的改变，增加或改变的图像，或者贡献一个新的文章，你需要Fork这个库，安装Git Bash批处理，并学习一些Git命令.

## 关于你对文档内容的贡献

### 轻微的修正

Minor corrections or clarifications you submit for documentation and code examples in this repo are covered by the [Azure Website Terms of Use (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


### 大的意见

If you submit a pull request with new or significant changes to documentation and code examples, we'll send a comment in GitHub asking you to submit an online Contribution License Agreement (CLA) if you are in one of these groups:

* Members of the Microsoft Open Technologies group.
* Contributors who don't work for Microsoft.

We need you to complete the online form before we can accept your pull request.

Full details are available at [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## 库的组织

The content in the azure-content repository follows the organization of documentation on [Azure.Microsoft.com](http://azure.microsoft.com). This repository contains two root folders:

### \articles

在 *\articles* folder contains the documentation articles formatted as markdown files with an *.md* extension.

Articles in the root directory are published to Azure.Microsoft.com in the path *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/*.

* **Article filenames:** See [our file naming guidance](./contributor-guide/file-names-and-locations.md).

Articles within their own service folder are published to Azure.Microsoft.com in the path
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*

* **Media subfolders:** The *\articles* folder contains the *\media* folder for root directory article media files, inside which are subfolders with the images for each article.  The service folders contain a separate media folder for the articles within each service folder. The article image folders are named identically to the article file, minus the *.md* file extension.

### \includes

You can create reusable content sections to be included in one or more articles. See [Custom extensions used in our technical content](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates

This folder contains our standard markdown template with the basic markdown formatting you need for an article.

### \contributor-guide

This folder contains articles that are part of our contributors' guide.  

## Use GitHub, Git, and this repository

For information about how to contribute, how to use the GitHub UI to contribute small changes, and how to fork and clone the repository for more significant contributions, see [Install and set up tools for authoring in GitHub](./contributor-guide/tools-and-setup.md).

If you install GitBash and choose to work locally, the steps for creating a new local working branch, making changes, and submitting the changes back to the main branch are listed in [Git commands for creating a new article or updating an existing article](./contributor-guide/git-commands-for-master.md)

### Branches

We recommend that you create local working branches that target a specific scope of change. Each branch should be limited to a single concept/article both to streamline work flow and reduce the possibility of merge conflicts.  The following efforts are of the appropriate scope for a new branch:

* A new article (and associated images)
* Spelling and grammar edits on an article.
* Applying a single formatting change across a large set of articles (e.g. new copyright footer).

## How to use markdown to format your topic

All the articles in this repository use GitHub flavored markdown.  Here's a list of resources.

- [Markdown basics](https://help.github.com/articles/markdown-basics/)

- [Printable markdown cheatsheet](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- For our list of markdown editors, see the [tools and setup topic](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Article metadata

Article metadata enables certain functionalities on the azure.microsoft.com web site, such as author attribution, contributor attribution, breadcrumbs, article descriptions, and SEO optimizations as well as reporting Microsoft uses to evaluate the performance of the content. So, the metadata is important! [Here's the guidance for making sure your metadata is done right](./contributor-guide/article-metadata.md).

## More resources

See the [index of our contributor's guide](./contributor-guide/contributor-guide-index.md) for all our guidance topics.
