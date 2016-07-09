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
* 你可以很容易的在GitHub的用户界面技术文章投稿。要么找到该知识库中的文章，或访问[文章官网](http://iuap.yonyou.com)并点击的文章中的文章GitHub源链接.
* 如果你是一个现有的文章作出实质性的改变，增加或改变的图像，或者贡献一个新的文章，你需要Fork这个库，安装Git Bash批处理，并学习一些Git命令.

## 关于你对文档内容的贡献

### 轻微的修正

Minor corrections or clarifications you submit for documentation and code examples in this repo are covered by the [Azure Website Terms of Use (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).
小的更正或澄清你提交的文档和代码在这个回购的例子是被使用的[ Azure网站条款（头）]

### 大的意见

If you submit a pull request with new or significant changes to documentation and code examples, we'll send a comment in GitHub asking you to submit an online Contribution License Agreement (CLA) if you are in one of these groups:

如果你提交pull请求与新增加的或重大变化的文档和示例代码，我们会在GitHub发送评论要求你提交一个在线投稿许可协议（CLA）如果你是其中的一个组：

* Members of the Microsoft Open Technologies group.
* Contributors who don't work for Microsoft.

*微软开放技术组的成员。
*贡献谁不为微软工作。

We need you to complete the online form before we can accept your pull request.

我们需要你完成的在线形式之前，我们可以接受你的拉要求。

Full details are available at [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

完整的细节是可用的

## 库的组织

The content in the azure-content repository follows the organization of documentation on [Azure.Microsoft.com](http://azure.microsoft.com). This repository contains two root folders:

在Azure的内容库的内容包括文档的组织[蔚蓝。微软。COM ]（http：/ /蔚蓝。微软。COM）。此存储库包含两个根文件夹：

### \articles

在 *\articles* folder contains the documentation articles formatted as markdown files with an *.md* extension.

Articles in the root directory are published to Azure.Microsoft.com in the path *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/*.

* **Article filenames:** See [our file naming guidance](./contributor-guide/file-names-and-locations.md).

Articles within their own service folder are published to Azure.Microsoft.com in the path
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*

* **Media subfolders:** The *\articles* folder contains the *\media* folder for root directory article media files, inside which are subfolders with the images for each article.  The service folders contain a separate media folder for the articles within each service folder. The article image folders are named identically to the article file, minus the *.md* file extension.

### \includes

You can create reusable content sections to be included in one or more articles. See [Custom extensions used in our technical content](./contributor-guide/custom-markdown-extensions.md).
您可以创建可重复使用的内容部分，将包含在一个或多个文章中。看到[用于我们的技术内容]自定义扩展（）。

### \markdown templates

This folder contains our standard markdown template with the basic markdown formatting you need for an article.
此文件夹包含标准减价模板与基本的Markdown格式你的文章需要。
### \contributor-guide

This folder contains articles that are part of our contributors' guide.  
此文件夹包含的文章是我们贡献者指南的一部分。
## Use GitHub, Git, and this repository

For information about how to contribute, how to use the GitHub UI to contribute small changes, and how to fork and clone the repository for more significant contributions, see [Install and set up tools for authoring in GitHub](./contributor-guide/tools-and-setup.md).

有关如何贡献，如何使用GitHub的UI贡献小的变化，以及如何叉和克隆更多的重大贡献的仓库，看到[安装和设置在GitHub ]创作工具（/投稿指南/工具和安装。MD）。

If you install GitBash and choose to work locally, the steps for creating a new local working branch, making changes, and submitting the changes back to the main branch are listed in [Git commands for creating a new article or updating an existing article](./contributor-guide/git-commands-for-master.md)

如果你安装gitbash选择在本地工作的步骤，创建一个新的地方工作科，做出改变，并将改变回到主分支被列入[ Git命令创建一个新的文章或更新现有的文章]（。/投稿指南/ git命令的主人。MD）。

### 分支

我们建议您创建针对特定范围的更改的本地工作分支。每一个分支应仅限于一个单一的概念/文章，以简化工作流程，减少合并冲突的可能性。下面的努力是一个新的分支适当的范围：

* A new article (and associated images)
* Spelling and grammar edits on an article.
* Applying a single formatting change across a large set of articles (e.g. new copyright footer).

*一个新的文章（和相关的图像）
*在一篇文章的拼写和语法编辑。
*将一个单一的格式更改应用在一个大的文章（例如，新的版权脚注）。

## 如何使用Markdown格式你的话题

All the articles in this repository use GitHub flavored markdown.  Here's a list of resources.
        这里有一个资源列表。
        
- [Markdown basics](https://help.github.com/articles/markdown-basics/)

- [Printable markdown cheatsheet](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- For our list of markdown editors, see the [tools and setup topic](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## 文章的元数据

Article metadata enables certain functionalities on the azure.microsoft.com web site, such as author attribution, contributor attribution, breadcrumbs, article descriptions, and SEO optimizations as well as reporting Microsoft uses to evaluate the performance of the content. So, the metadata is important! [Here's the guidance for making sure your metadata is done right](./contributor-guide/article-metadata.md).

文章的元数据使某些功能在azure.microsoft.com网站，如作者归属，原因归因、面包糠、文章的描述，和SEO优化以及微软使用评价报告内容的表现。因此，元数据是重要的！[这里的指导，确保您的元数据做正确的]（/投稿指南/文章元数据）。
## 更多的资源

请查看[产品官方网站](http://iuap.yonyou.com)。
