---
  layout: post
  title: Organising your Git repository
  tags: git management
  categories: 
  lang: en
  ref: managing-git
---

Managing your code repository should be one of the main tasks during the lifetime of the project. However, it could happen that not every developer works the same way, making it vital to create a space for sharing and setting some guidelines and rules among projects inside the company. Failing to do this, could lead to mistakes due to different interpretations from developers, increased by the period of lifetime of the project and the entering/exiting of multiple developers.

I worked once on an Android project where its master branch had not been updated for almost two years, even though there had been several releases uploaded to GooglePlay. Because of this, when a contractor came in to make some work, he branch off master, losing years of work available in the develop branch. When this company contracted me, they told me they had noticed that there were some errors that had been already fixed and some missing features...how not?! This is a good example of how something really simple as updating and keeping a repository tidy could cost time (and money) if we lose sight of it.

One tool that has helped me managing better my repositories is **[SourceTree, de Atlassian](https://www.sourcetreeapp.com/)** *-yes, I use a software for handling git instead of the terminal*. Personally, I think it's one of the best tools for a developer because in a few clicks you can clone or create a repository, visually grouping them by companies, projects, see every branch, create new stashes (saves the current status of your local copy, in case you are developing your shiny new feature and someone realises there is an error in production you need to fix right away), easy implementation of GitFlow, search between your commits...As they say in their website; simple for beginners, powerful for experts. In order to download it, you must create an account in Atlassian and activate the free license they offer with it.

![SourceTree window](../../../assets/images/managing-git/sourcetree-screen.png "SourceTree main screen ")

For some time now, every project I've worked on have followed GitFlow, original idea from **[Vincent Driessen](http://nvie.com/posts/a-successful-git-branching-model/)**, which is the way I would recommend to follow for keeping a clean and tidy repository.

SourceTree contains GitFlow integration and allows you to initiate a repository in just a few clicks *-Repository > Git flow/Hg flow > Initialize repository-*. This process automatically creates a develop branch and the *"folders"* that will store the rest of the transient branches you will need during the lifetime of the project. If you wanted to, you could even change the name of this temporal branch folders, but I wouldn't recommend it as leaving it by default will increase the probabilities that the next developer to join the project knows how to use every branch.

![Initiating Gitflow with SourceTree](../../../assets/images/managing-git/creating-gitflow.png "Initiating GitFlow")

The branch strategy GitFlow suggestion is the following:

  - **Master:** This is the main branch and it stores code that has been already published . It should always be tagged for an easy identification of releases.

  - **Develop:** This branch serves as integration for any new functionality before wrapping up a new version.

  - **Feature:** For implementation of new features or non-urgent bug fixing. These branches always branch off the develop branch an should have a short period of life -reason why we create a new branch for each task- and are merge back to develop once the feature has been successfully finished. The naming convention I tend to use is *feature/{developer-initials}-{task-name}*, *eg. feature/ede-myNewFeature*.

  - **Release:** After finishing every feature we want to include in a new release and have merged back into develop, a release branch is created, increasing the version number. This code is deployed and tested and if any error were found, developers would fix it and commit to the same release branch. This should be an iterative process till everybody is happy to continue publishing a new version. At this moment, we would merge into the master and develop branches, so we are sure the changes done during the publishing process are not accidentally overwritten due to some new development. A valid naming convention could be *release/{version-to-deploy}-{date}*, eg. *release/1.2.0-11082017*.
  
  - **Hotfix:** These branches are created to fix urgent errores, as they occur in a public version *-normally, you tend to fix in this way errors that make your app crash*. Because of that, these branches branch off master and, after successfully fixing the problem, they are merge back into master and develop, for making sure the changes are not overwritten with other developments.

![GitFlow workflow](../../../assets/images/managing-git/gitflow-diagram.png "GitFlow workflow example")  

There are a few different models to manage your Git repository but GitFlow, although it has its own inconvenient, is one of the most common models in development. If you are not currently following any clear strategy, I would strongly recommend you to try it out!

<br>
<br>
<br>

***
For more information about other models and problems of GitFlow, you can read the following external links:
- **[Comparing Workflows, by Atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow)**
- **[Reconsidering GitFlow](http://endoflineblog.com/gitflow-considered-harmful)**
- **[GitHub Flow](https://guides.github.com/introduction/flow/)**

***