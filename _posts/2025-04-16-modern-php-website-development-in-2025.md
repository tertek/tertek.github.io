---
tags: php
---

# Modern PHP website development in 2025

This is a guide on modern PHP website development. Not always you will need to build a web application, but only a website that consumes a data source and displays it as page or site. Of course it may include features as forms, which means it has more capabilities than simple websites, but still it is not enough to count as an application.

While there are many PHP frameworks that help you to build cutting-edge web applications, there seems to be a lack of guides on how to build websites using modern technology, without starting from scratch or using a framework that has too many features. This guide covers the bare minimum with setting up your development environment, building your own **website template** and finally reusing this for your projects.

## Requirements

This guide assumes that you are using Linux and that you have pre-installed necessary pre-requisites:

- Git

- Docker

- Composer

## Setup Development Environment

The classical approach to developing PHP websites on Windows or MacOS has always been using a tool that emulates a LEMP/LAMP environment, where PHP will run seamlessly. Well known tools are MAMP, XAMPP or Laragon. Since all of these tools have their different advantages and compatibilities, there was never a real winner between them. Another option is to use virtual machines or directly setup a webserver on install PHP on Linux. But since the virtualization through Docker has become much easier than ever, a more lightweight approach has been achieved for PHP Development. 

These new tools are build upon Docker and abstact the whole docker handling away, enabling the developer to focus on just spinning up the services they need through simple CLI commands.

Currently, most prominent tools that can be ussed accross PHP projects are:

- [ddev](https://ddev.com/): Docker-based PHP development environments

- [lando](https://lando.dev): Instant dev environments for all your projects.

- [docksal](https://docksal.io): All-purpose web-development environment based on Docker and Docker Compose. (MacOs)

This guide focuses on ddev usage, since it seems most straighforward to bootstrap a project and has a lightweight CLI syntax. But feel free to use any other tool.

A simple way to install ddev with the install script:

```bash
# Download and run the install script
curl -fsSL https://ddev.com/install.sh | bash
# https://ddev.readthedocs.io/en/stable/users/install/ddev-installation/#linux
```

That's it. Now you are ready to setup your developement template.

## Setup your website template

One of the most important development principle is [DRY](https://www.digitalocean.com/community/tutorials/what-is-dry-development), which means "don't repeat yourself". This is not only relevant for the code you write within one project, but also for steps between multiple projects. That is why it makes sense to setup your development templates, in case you are working on projects that do not require a framework and are similar to each other.

An example for such a case could be the development of dynamically rendered websites that consume a decoupled [headless CMS](https://en.wikipedia.org/wiki/Headless_content_management_system). In the best scenario an API  can be accessed through HTTP from the PHP Website providing authentication credentials. So what is needed is a solid base that will take over the data pulling, routing the data to a view, where it will be rendered using a template engine and finally bundleded with a modern CSS library. 

Of course you could go directly with a framework such as Laravel or Slim, but what if you really only want to use the bare minimum without all the other useful but unnecessary stuff in this case? This guide will specifically focus on using framework-level components to build a minimal and lightweight website.

In most cases, the only thing that changes from project to project, is the data structure and design of the views, which means the whole skeleton of the website can be reused.

This guide will show an example of builing such a reusable modern PHP development template, based on a DDEV development environment, [Illuminate classes](https://github.com/illuminate) and [TailwindCSS](https://tailwindcss.com/) frontend library.

### 1. Create a generic PHP project with ddev

Create a project directory and then run ddev inside the directory. We will need a generic PHP project, without the use of database or SSH agent containers.

```bash
mkdir developement-template
cd developement-template
ddev config --project-type=generic --omit-containers=db,ddev-ssh-agent
```

### 2. Create project structure

Before starting, let's initiate composer:

```bash
ddev composer init
# follow and respond to the interactive dialog to your needs
```

Your project structure should allow to bundle resources into a public directory where they will be served. In general it makes sense to be inspired by structures of popular frameworks. If one of the following directories or files is missing, simply add it to your project:

```bash
- .ddev # configuration file for ddev, created with `ddev config`
- package.json # configuration file for composer, created with `composer init`
- public # will be used to serve bundleded resourced
- resources # is the source to be bundldeded
-- css/app.css
-- js/app.js
- index.php # entry point of the website
```

### 3. Setup TailwindCSS with Laravel Mix

Original Source: [Install Tailwind CSS with Laravel - Tailwind CSS](https://tailwindcss.com/docs/installation/framework-guides/laravel/mix)

```bash
ddev npm install tailwindcss @tailwindcss/postcss postcss
```

Add in your `webpack.mix.js` 

```js
mix
  .js("resources/js/app.js", "public/js")
  .postCss("resources/css/app.css", "public/css", [
    require("@tailwindcss/postcss"),
  ]);
```

Add in your `/resources/css/app.css`

```css
@import "tailwindcss";
```

Compile the assets by running mix:

```bash
ddev npx mix
```

Additionally, you can adjust your `package.json` with a `script` tag:

```json
  "scripts": {
    "dev": "npx mix"
  },
```

This will allow you to run mix with `ddev npm run dev`.

Finally, load your assets into your `index.php`:

```html
<!doctype html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link href="/public/css/app.css" rel="stylesheet" type="text/css">
</head>
<body>
  <h1 class="text-3xl font-bold underline">
    Hello world!
  </h1>
  <script src="/public/js/app.js"></script>
</body>
</html>
```

### 4. Setup Views and Routing

There are many template engines out there, such as Twig. But why not stick to something that you allready may use with bigger projects, such as Laravel Blade? It is possible to use Laravel views outside of the framework, since they are based in Illuminate classes.

The [Torch project ](https://github.com/mattstauffer/Torch/tree/master)is a very helpful guide on how to use Illuminate classes outisde of Laravel. We would like to have functionalities of Views and Routing. 

**This guide is work in progress and will be updated within the next time.**
