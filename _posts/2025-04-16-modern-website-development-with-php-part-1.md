---
tags: php
---

# Modern website development with PHP (Part 1)

Not always you will need to build a web application, but only a website that consumes a data source and displays it as page or site. Of course it may include features as forms, which means it has more capabilities than simple websites, but still it is not enough to count as an application.

While there are many PHP frameworks that help you to build cutting-edge web applications, there seems to be a lack of guides on how to build websites using modern technology, without starting from scratch or using a framework that has too many features. 

This guide explains the bare minimum of setting up your development environment, integrating your necessary build tools and creating your own **minimal framework**.

The first part specifies the development objective and the defines the features of the framework we would like to achieve. Based on that, we will setup a docker-based modern development environment. Finally, in the last section of this part, we will define an initial project structure that will serve as a foundation of the framework to be created.

- **Part 1: Setting up development environment and project structure**
- [Part 2: Integrating front-end build tools and libraries](#tbd)
- [Part 3: Creating a component-based minimal framework](#tbd)


## Development objective

One of the most important development principles is [DRY](https://www.digitalocean.com/community/tutorials/what-is-dry-development), which means "don't repeat yourself". This is not only relevant for the code you write within one project, but also for activities between multiple projects. That is why it makes sense to create your own framework, in case you are working on projects that do not require a feature-rich framework.

An example for such a case could be the development of dynamically rendered websites that consume a decoupled [Headless CMS](https://en.wikipedia.org/wiki/Headless_content_management_system). 

Of course you could go directly with a framework such as [Laravel](https://laravel.com/) or [Slim](https://www.slimframework.com/). However, what if you really only want to use the bare minimum - without all the other useful - but unnecessary stuff in this case? 

The following is an opinionated, minimal list of features for a (decoupled) modern website:
- Pulls data of variable structure from external sources (API) and renders the content dynamically on different pages
- Displays data in reusable views using modern style and layout standards
- Supports multilingual content
- Supports form submissions 

To achieve above features, following components would be essential as part of our framework:
- Front Controller: Handles request and response, may be extended by middleware for additional features.
- Data Mapper: Helps to pull external data sources and bind it to the actual website's context.
- Router: Handles which content is shown for each request and response.
- Template Engine: Displays your content through views with reusable components and layouts, adds chaching capabilities.

In this example we will build our own framework, based on a DDEV development environment, standard PHP components using [Smyfony packages](https://symfony.com/) and modern styling using [TailwindCSS](https://tailwindcss.com/). The final project can be found in this Github repository: [tertek/new-php-framework](https://github.com/tertek/new-php-framework).

## Requirements

It is assumed that you are using Linux and that you have installed necessary pre-requisites:

- [Git](https://git-scm.com/)
- [Docker](https://docs.docker.com/engine/install/)

## Setup Development Environment with DDEV

The classical approach to developing PHP websites on Windows or MacOS has always been using a tool that emulates a LEMP/LAMP environment, where PHP will run seamlessly. Well known tools are MAMP, XAMPP or Laragon. Since all of these tools have their different advantages and compatibilities, there was never a real winner between them. Another option is to use virtual machines or to directly setup a webserver on barebones Linux. But since the virtualization through Docker has become much easier than ever, a more lightweight approach has been achieved for PHP Development. 

These new tools are built upon Docker and abstract the whole container handling away, enabling developers to focus on just spinning up the services that they need through a simple CLI.

Currently, most prominent tools that can be ussed accross PHP projects are:

- [ddev](https://ddev.com/): Docker-based PHP development environments.
- [lando](https://lando.dev): Instant dev environments for all your projects.
- [docksal](https://docksal.io): All-purpose web-development environment based on Docker and Docker Compose. (MacOs)

I chose ddev since it seems most straighforward to bootstrap a project and has a lightweight & well-documented CLI. Feel free to use any other tool though.

A simple way to install ddev with the install script:

```bash
# Download and run the install script
curl -fsSL https://ddev.com/install.sh | bash
# https://ddev.readthedocs.io/en/stable/users/install/ddev-installation/#linux
```

Now we are ready to build in containerized PHP. DDEV supports different project types to start with. Since we are building our framework from scratch, we will start with a generic PHP project.


Create a new project directory and name it as you want your framework to be called. Inside the directory run ddev config specified to our needs:

```bash
mkdir new-php-framework
cd new-php-framework
ddev config --project-name=new-php-framework --project-type=generic --omit-containers=db,ddev-ssh-agent --webserver-type=apache-fpm
```
The `ddev config` command configures a new ddev environment that can be managed through a configuration file at `/.ddev/config.yaml`. We are passing some flags with the command that sets the configuration on the fly: `--project-name=new-php-framework` provides the project name of project to configure which should be the same as the directory name. `--project-type=generic` will only include minimal required files for a generic PHP project (ddev also supports boilerplate for frameworks such as Laravel or Symfony). `--omit-containers=db,ddev-ssh-agent` removes containers from the docker compose, since we do not need a database and also not an SSH agent for small projects. Finally, `--webserver-type=apache-fpm` changes the webserver type from default Nginx to Apache, because the latter is easier to configure through `.htaccess` files on shared hostings, which is often the preferred deployment option for simple website projects (but feel free to stay with Nginx if it makes more sense for you).

Once, you have run above command you should receive the following confirmation from ddev:

```bash
Creating a new DDEV project config in the current directory (/home/tertek/projects/new-php-framework) 
Once completed, your configuration will be written to /home/tertek/projects/new-php-framework/.ddev/config.yaml
 
Configuring a 'generic' project named 'new-php-framework' with docroot '' at '/home/tertek/projects/new-php-framework'.
For full details use 'ddev describe'. 
Configuration complete. You may now run 'ddev start'. 

```

Before we start ddev, let's first add our essential framework structure.

### Setup framework structure

The framework structure is essential because it defines how the different components *can* interact with each other. The structure is also critical when it comes to setup frontend libraries and get them running in a pipeline, that is enjoyable for the developers. The following steps begin to setup the major part of the framework structure, which will be extended over the next sections by component relevant parts.

To be able to use composer in the project let's initiate it. Follow and respond to the interactive dialog to your needs. You should not define any dependencies at this point yet.

```bash
ddev composer init
```
Next, create the following directories in your project's root:

**/bootstrap**: Includes everything we need to spin up our framework. Normally, is included as a file from `bootstrap/app.php` within `public/index.php`.

**/pages**: This is where our project specific pages will be included. The framework will understand that the data fetched has to be routed with the pages in this directory.

**/public**: Is the main entry point of our website and only publicly accessible path on the webserver.

**/resources**: Here we will keep all files that are required for styling and scripting our pages, such as CSS, JavaScript, images and fonts.

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
if(file_exists(__DIR__ . '/../vendor/autoload.php')) {
    require __DIR__ . '/../vendor/autoload.php';
}

// Bootstrap the framework..
require_once __DIR__ . '/../bootstrap/app.php';
```

`bootstrap/app.php`, include simple home page - no routing yet.

```php
<?php

//  Include a home page for now..
include __DIR__ . '/../pages/home.php';
```

`pages/home.php`, echo 'Hello World'.

```php
<?php

echo 'Hello World';
```

Now that we have the basic structure of our framework ready, let's run it and see if it works:

`ddev launch` will start ddev, build required containers and launch your project on your localhost. It will auto-configure your `etc/host` and create local urls as <project>.ddev.site. Navigating to the site's url should show you a plain page with a 'Hello World'.


### Next


In the next part of this series we will integrate frontend build tools and libraries to the framework. 