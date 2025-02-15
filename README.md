[![license](https://img.shields.io/github/license/peaceiris/actions-gh-pages.svg)](https://github.com/peaceiris/actions-gh-pages/blob/master/LICENSE)
[![release](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg)](https://github.com/peaceiris/actions-gh-pages/releases/latest)
[![GitHub release date](https://img.shields.io/github/release-date/peaceiris/actions-gh-pages.svg)](https://github.com/peaceiris/actions-gh-pages/releases)
[![GitHub Actions status](https://github.com/peaceiris/actions-gh-pages/workflows/docker%20image%20ci/badge.svg)](https://github.com/peaceiris/actions-gh-pages/actions)
[![Docker Hub Build Status](https://img.shields.io/docker/cloud/build/peaceiris/gh-pages.svg)](https://hub.docker.com/r/peaceiris/gh-pages)

<img width="400" alt="GitHub Actions for deploying to GitHub Pages with Static Site Generators" src="./images/ogp.svg">



## GitHub Actions for GitHub Pages

This is a **GitHub Action** to deploy your static files to **GitHub Pages**.
This deploy action can be combined simply and freely with [Static Site Generators]. (Hugo, MkDocs, Gatsby, GitBook, etc.)

[Static Site Generators]: https://www.staticgen.com/

```yaml
- name: Deploy
  uses: peaceiris/actions-gh-pages@v2.4.0
  env:
    ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
    PUBLISH_BRANCH: gh-pages
    PUBLISH_DIR: ./public
```

The above example step will deploy `./public` directory to `gh-pages` branch.



## Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Getting started](#getting-started)
  - [(1) Add SSH deploy key](#1-add-ssh-deploy-key)
  - [(2) Create your workflow](#2-create-your-workflow)
    - [⭐️ Repository type - Project](#%EF%B8%8F-repository-type---project)
    - [⭐️ Repository type - User and Organization](#%EF%B8%8F-repository-type---user-and-organization)
- [Options](#options)
  - [⭐️ Pull action image from Docker Hub](#%EF%B8%8F-pull-action-image-from-docker-hub)
  - [⭐️ `PERSONAL_TOKEN`](#%EF%B8%8F-personal_token)
  - [⭐️ `GITHUB_TOKEN`](#%EF%B8%8F-github_token)
  - [⭐️ Suppressing empty commits](#%EF%B8%8F-suppressing-empty-commits)
  - [⭐️ Keeping existing files](#%EF%B8%8F-keeping-existing-files)
- [Tips and FAQ](#tips-and-faq)
  - [⭐️ Use the latest and specific release](#%EF%B8%8F-use-the-latest-and-specific-release)
  - [⭐️ How to add `CNAME`](#%EF%B8%8F-how-to-add-cname)
  - [⭐️ Deployment completed but you cannot read](#%EF%B8%8F-deployment-completed-but-you-cannot-read)
- [Examples](#examples)
  - [⭐️ Static Site Generators with Node.js](#%EF%B8%8F-static-site-generators-with-nodejs)
  - [⭐️ Gatsby](#%EF%B8%8F-gatsby)
  - [⭐️ React and Next](#%EF%B8%8F-react-and-next)
  - [⭐️ Vue and Nuxt](#%EF%B8%8F-vue-and-nuxt)
  - [⭐️ Static Site Generators with Python](#%EF%B8%8F-static-site-generators-with-python)
- [License](#license)
- [About the author](#about-the-author)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



## Getting started

### (1) Add SSH deploy key

Generate your deploy key with the following command.

```sh
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
# You will get 2 files:
#   gh-pages.pub (public key)
#   gh-pages     (private key)
```

Next, Go to **Repository Settings**

- Go to **Deploy Keys** and add your public key with the **Allow write access**
- Go to **Secrets** and add your private key as `ACTIONS_DEPLOY_KEY`

| Add your public key | Success |
|---|---|
| ![](./images/deploy-keys-1.jpg) | ![](./images/deploy-keys-2.jpg) |

| Add your private key | Success |
|---|---|
| ![](./images/secrets-1.jpg) | ![](./images/secrets-2.jpg) |

### (2) Create your workflow

Add your workflow setting YAML file `.github/workflows/gh-pages.yml` and push to the default branch.

#### ⭐️ Repository type - Project

An example workflow for Hugo.

- [peaceiris/actions-hugo: GitHub Actions for Hugo](https://github.com/peaceiris/actions-hugo)

[![peaceiris/actions-hugo - GitHub](https://gh-card.dev/repos/peaceiris/actions-hugo.svg?fullname)](https://github.com/peaceiris/actions-hugo)

![peaceiris/actions-hugo latest version](https://img.shields.io/github/release/peaceiris/actions-hugo.svg?label=peaceiris%2Factions-hugo)
![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master
      # with:
      #   submodules: true

    - name: Setup Hugo
      uses: peaceiris/actions-hugo@v2.2.1
      with:
        hugo-version: '0.58.3'

    - name: Build
      run: hugo --gc --minify --cleanDestinationDir

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v2.4.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./public
```

The above example is for [Project Pages sites]. (`<username>/<project_name>` repository)

| Actions log overview | Build step log |
|---|---|
| ![](./images/log1.jpg) | ![](./images/log2.jpg) |

| Deploy step log | GitHub Pages log |
|---|---|
| ![](./images/log3.jpg) | ![](./images/log4.jpg) |

#### ⭐️ Repository type - User and Organization

For [User and Organization Pages sites] (`<username>/<username>.github.io` repository),
we have to set `master` branch to `PUBLISH_BRANCH`.

```yaml
on:
  push:
    branches:
    - source  # default branch

PUBLISH_BRANCH: master  # deploying branch
```

[Project Pages sites]: https://help.github.com/en/articles/user-organization-and-project-pages#project-pages-sites
[User and Organization Pages sites]: https://help.github.com/en/articles/user-organization-and-project-pages#user-and-organization-pages-sites

![Change default branch](./images/default-branch.jpg)

<div align="right">
<a href="#table-of-contents">Back to TOC ☝️</a>
</div>



## Options

### ⭐️ Pull action image from Docker Hub

You can pull a public docker image from Docker Hub.
By pulling docker images, you can reduce the overall execution time of your workflow. In addition, `latest` tag is provided.

```diff
- uses: peaceiris/actions-gh-pages@v2.4.0
+ uses: docker://peaceiris/gh-pages:v2.4.0
```

- [peaceiris/gh-pages - Docker Hub](https://hub.docker.com/r/peaceiris/gh-pages)

### ⭐️ `PERSONAL_TOKEN`

[Generate a personal access token (`repo`)](https://github.com/settings/tokens) and add it to Secrets as `PERSONAL_TOKEN`, it works as well as `ACTIONS_DEPLOY_KEY`.

```diff
- ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
+ PERSONAL_TOKEN: ${{ secrets.PERSONAL_TOKEN }}
```

### ⭐️ `GITHUB_TOKEN`

> **NOTES**: Do not use `GITHUB_TOKEN`.
>
> This action supports `GITHUB_TOKEN` but it has some problems to deploy to GitHub Pages. GitHub team is investigating that. See [Issue #9]

[Issue #9]: https://github.com/peaceiris/actions-gh-pages/issues/9

```diff
- ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
+ GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### ⭐️ Suppressing empty commits

By default, a commit will always be generated and pushed to the `PUBLISH_BRANCH`, even if nothing changed. If you want to suppress this behavior, set the optional parameter `emptyCommits` to `false`. cf. [Issue #21]

[Issue #21]: https://github.com/peaceiris/actions-gh-pages/issues/21

For example:

```yaml
- name: Deploy
  uses: peaceiris/actions-gh-pages@v2.4.0
  env:
    ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
    PUBLISH_BRANCH: gh-pages
    PUBLISH_DIR: ./public
  with:
    emptyCommits: false
```

### ⭐️ Keeping existing files

By default, existing files in the publish branch are removed before adding the ones from publish dir. If you want the action to add new files but leave existing ones untouched, set the optional parameter `keepFiles` to `true`.

For example:

```yaml
- name: Deploy
  uses: peaceiris/actions-gh-pages@v2.4.0
  env:
    ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
    PUBLISH_BRANCH: gh-pages
    PUBLISH_DIR: ./public
  with:
    keepFiles: true
```

<div align="right">
<a href="#table-of-contents">Back to TOC ☝️</a>
</div>



## Tips and FAQ

### ⭐️ Use the latest and specific release

We recommend you to use the latest and specific release of this action for stable CI/CD.
It is useful to watch this repository (release only) to check the [latest release] of this action.

[latest release]: https://github.com/peaceiris/actions-gh-pages/releases

### ⭐️ How to add `CNAME`

Most of the Static Site Generators support `CNAME` as a static file.

- [Use a Custom Domain | Hugo](https://gohugo.io/hosting-and-deployment/hosting-on-github/#use-a-custom-domain)
- [Using the Static folder | GatsbyJS](https://www.gatsbyjs.org/docs/static-folder/)

The same may be said of other files (`.nojekyll`, `BingSiteAuth.xml`, `robots.txt`, etc.). It is better to manage those files by Static Site Generators.

Does not your static site generator deal with the static files? No problem, you can add the file like the following.

```yaml
- name: Build
  run: |
    buildcommand
    cp ./path/to/CNAME ./public/CNAME

- name: Deploy
```

### ⭐️ Deployment completed but you cannot read

Does your `PUBLISH_DIR` contain files or directories that name starts with an underscore? (`_modules`, `_sources` and `_next`, etc.)
GitHub Pages does not read those by default.
Please add `.nojekyll` file to `PUBLISH_DIR`.

- [Bypassing Jekyll on GitHub Pages - The GitHub Blog](https://github.blog/2009-12-29-bypassing-jekyll-on-github-pages/)

> It is now possible to completely bypass Jekyll processing on GitHub Pages by creating a file named `.nojekyll` in the root of your pages repo and pushing it to GitHub. This should only be necessary if your site uses files or directories that start with underscores since Jekyll considers these to be special resources and does not copy them to the final site.

Does not your static site generator deal with the static files? No problem, you can add the file like the following.

```yaml
- name: Build
  run: |
    buildcommand
    touch ./public/.nojekyll

- name: Deploy
```

<div align="right">
<a href="#table-of-contents">Back to TOC ☝️</a>
</div>



## Examples

### ⭐️ Static Site Generators with Node.js

[hexo], [gitbook], [vuepress], [react-static], [gridsome], etc.

[hexo]: https://github.com/hexojs/hexo
[gitbook]: https://github.com/GitbookIO/gitbook
[vuepress]: https://github.com/vuejs/vuepress
[react-static]: https://github.com/react-static/react-static
[gridsome]: https://github.com/gridsome/gridsome

Premise: Dependencies are managed by `package.json` and `package-lock.json`

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: build
      uses: actions/setup-node@v1
      with:
        node-version: '10.16'
    - run: |
        npm install
        npm run build

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.4.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./public
```

### ⭐️ Gatsby

An example for [Gatsby] (Gatsby.js) project with [gatsby-starter-blog]

[Gatsby]: https://github.com/gatsbyjs/gatsby
[gatsby-starter-blog]: https://github.com/gatsbyjs/gatsby-starter-blog

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: setup node
      uses: actions/setup-node@v1
      with:
        node-version: '10.16'

    - name: install
      run: npm install

    - name: format
      run: npm run format

    - name: test
      run: npm run test

    - name: build
      run: npm run build

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.4.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./public
```

### ⭐️ React and Next

An example for [Next.js] (React.js) project with [create-next-app]

- cf. [Deploying a Next.js app into GitHub Pages · zeit/next.js Wiki](https://github.com/zeit/next.js/wiki/Deploying-a-Next.js-app-into-GitHub-Pages)

[Next.js]: https://github.com/zeit/next.js
[create-next-app]: https://nextjs.org/docs

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: setup node
      uses: actions/setup-node@v1
      with:
        node-version: '10.16'

    - name: install
      run: yarn install

    - name: build
      run: yarn build

    - name: export
      run: yarn export

    - name: add nojekyll
      run: touch ./out/.nojekyll

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.4.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./out
```

### ⭐️ Vue and Nuxt

An example for [Nuxt.js] (Vue.js) project with [create-nuxt-app]

- cf. [GitHub Pages Deployment - Nuxt.js](https://nuxtjs.org/faq/github-pages)

[Nuxt.js]: https://github.com/nuxt/nuxt.js
[create-nuxt-app]: https://github.com/nuxt/create-nuxt-app

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: setup node
      uses: actions/setup-node@v1
      with:
        node-version: '10.16'

    - name: install
      run: npm install

    - name: test
      run: npm test

    - name: generate
      run: npm run generate

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.4.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./dist
```

### ⭐️ Static Site Generators with Python

[pelican], [MkDocs], [sphinx], etc.

[pelican]: https://github.com/getpelican/pelican
[MkDocs]: https://github.com/mkdocs/mkdocs
[sphinx]: https://github.com/sphinx-doc/sphinx

Premise: Dependencies are managed by `requirements.txt`

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@v1

    - name: Set up Python
      uses: actions/setup-python@v1
      with:
        python-version: '3.6'
        architecture: 'x64'

    - name: Install dependencies
      run: |
        pip install --upgrade pip
        pip install -r ./requirements.txt

    - name: Build
      run: mkdocs build

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v2.4.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./site
```



## License

- [MIT License - peaceiris/actions-gh-pages]

[MIT License - peaceiris/actions-gh-pages]: https://github.com/peaceiris/actions-gh-pages/blob/master/LICENSE



## About the author

- [peaceiris's homepage](https://peaceiris.com/)



<div align="right">
<a href="#table-of-contents">Back to TOC ☝️</a>
</div>
