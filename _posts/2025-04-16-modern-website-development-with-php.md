---
tags: php
---

# Modern website development with PHP
This is a guide on modern PHP website development. Not always you will need to build a web application, but only a website that consumes a data source and displays it as page or site. Of course it may include features as forms, which means it has more capabilities than simple websites, but still it is not enough to count as an application.

While there are many PHP frameworks that help you to build cutting-edge web applications, there seems to be a lack of guides on how to build websites using modern technology, without starting from scratch or using a framework that has too many features. This guide covers the bare minimum with setting up your development environment, building your own **minimal framework** and finally reusing this for your projects.

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

That's it. Now you are ready to create your own framework.

## Create your framework

One of the most important development principle is [DRY](https://www.digitalocean.com/community/tutorials/what-is-dry-development), which means "don't repeat yourself". This is not only relevant for the code you write within one project, but also for the activities between multiple projects. That is why it makes sense to create your own framework, in case you are working on projects that do not require a feature-rich framework.

An example for such a case could be the development of dynamically rendered websites that consume a decoupled [Headless CMS](https://en.wikipedia.org/wiki/Headless_content_management_system). In the best scenario an API  can be accessed through HTTP from the PHP Website providing authentication credentials. 
g [Smyfony packages](https://symfony.com/) and modern s

Of course you could go directly with a framework such as Laravel or Slim, but what if you really only want to use the bare minimum without all the other useful - but unnecessary stuff in this case? This guide will specifically focus on using essential components to build a minimal and lightweight framework.

The following is an opinionated minimal selection of features for a decoupled modern website:
- Pulls data of variable structure from external sources and renders the content dynamically on different pages
- Displays data in reusable views using modern style and layout standards
- Supports multilingual content
- Supports form submissions 

To achieve above features, following components would be essential as part of our framework:
- Frontcontroller: Handles the request and response
- Data Mapper: Maps external data sources to the actual website's context
- Router: Maps URLs to what is rendered as response
- Template Engine: Build your views with reusable components and layouts, handling data with more readable syntax

This guide will show an example of builing our own framework, based on a DDEV development environment, standard PHP components using [Smyfony packages](https://symfony.com/) and modern styling using [TailwindCSS](https://tailwindcss.com/).

### 1. Create a generic PHP project with ddev

Create a new project directory and name it as you want your framework to be called. Inside the directory run ddev config specified to our needs:

```bash
mkdir new-php-framework
cd new-php-framework
ddev config --project-name=new-php-framework --project-type=generic --omit-containers=db,ddev-ssh-agent --webserver-type=apache-fpm
```
The `ddev config` command configures a new ddev environment that can be managed through a configuration file at `/.ddev/config.yaml`. We are passing some flags with the command that sets the configuration on the fly: `--project-name=new-php-framework` provides the project name of project to configure which should be the same as the directory name. `--project-type=generic` will only include minimal required files for a generic PHP project (ddev also supports boilerplate for frameworks such as Laravel or Symfony). `--omit-containers=db,ddev-ssh-agent` removes containers from the docker compose, since we do not need a database and also not an SSH agent for small projects. Finally, `--webserver-type=apache-fpm` changes the webserver type from default Nginx to Apache, because the latter is easier to configure through `.htaccess` files on shared hostings, which is often the preferred deployment option for simple website projects (but feel free to stay with Nginx if it makes more sense for you).

### 2. Setup framework structure

Before starting, let's initiate composer:

```bash
ddev composer init
# follow and respond to the interactive dialog to your needs
```

Next create the following directory structure in your directory root:

```bash
- bootstrap
- pages
- public
- resources
```

**bootstrap**: Includes everything we need to spin up our framework. Normally, is included as a file from `bootstrap/app.php` within `public/index.php`.

**pages**: This is where our project specific pages will be included. The framework will understand that the data fetched has to be routed with the pages in this directory.

**public**: Is the main entry point of our website and only publicly accessible resource on the webserver.

**resources**: Here we will keep all files that are required for styling and scripting our pages, such as CSS, JavaScript, images and fonts.

So to achieve this, we will add the following files, beginning with the entrypoint:

`.htaccess`, rewriting the index of the webserver to `public/index.php`:

```
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_URI} !^/public/
    RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
```

`public/index.php`, including composer autoloader and framework bootstrap file.

```php
<?php

// Register the Composer autoloader...
require __DIR__ . '/../vendor/autoload.php';

// Bootstrap DOA and handle the request...
$app = require_once __DIR__ . '/../bootstrap/app.php';

```

`bootstrap/app.php`, 

```php
<?php

//  For the beginning we will only include a static home page
include _DIR__ . '/../pages/home.php';

```

`pages/home.php`

```php
<?php

echo 'Hello World';
```

Now that we have the basic structure of our framework ready, let's run it and see if it works:

`ddev launch` will start ddev, build required containers and launch your project on your localhost. It will auto-configure your `etc/host` and create local urls as <project>.ddev.site. Navigating to the url should show you a plain page with a 'Hello World'.


### 3. Setup TailwindCSS with Laravel Mix

The installation of TailwindCSS can be achieved easily when we use Laravel Mix as a helper above Webpack. You could also Vite, but it is a bit more cumbersome to setup with PostCSS (we want to use CSS and not SCSS). The following is an alignment to ddev of the official documentation to [Install Tailwind CSS with Laravel - Tailwind CSS + Laravel Mix](https://tailwindcss.com/docs/installation/framework-guides/laravel/mix).

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
  }
```

This will allow you to run mix with `ddev npm run dev`.

Finally, load your assets into your page at `pages/home.php`:

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

**This guide is work in progress and will be updated within the next time.**
