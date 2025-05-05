---
tags: php
---

# Modern website development with PHP - 2

This article is the second part of a series on modern PHP website development. In the first part we have setup a development environment with DDEV and started with our framework's project structure. In this part we will integrate frontend build tools and libraries.

- [Part 1: Setting up development environment and project structure](/2025/04/16/modern-website-development-with-php-part-1.html)
- **Part 2: Integrating front-end build tools and libraries**
- [Part 3: Creating a component-based minimal framework](#tbd)

## Introduction

The integration of front-end build tools and libraries is essential for modern web development and thus for website development. By the use of front-end libraries we can accelerate the process of website development, create our own custom components to be re-used and be in sync with current design paradigms and principles. 

Without going into details of the why and what, we will use [TailwindCSS](https://tailwindcss.com/) as our CSS library/framework (it is hard to term correctly at this level of the conversation, since the makers of Tailwind call it a utility-first framework, so we cannot disrespect that).

In order to integrate front-end libraries (or frameworks) such as TailwindCSS into our PHP framework, it is recommended to use a build tool. Build tools help during development and building the actual assets. We will use [Vite](https://vite.dev/) as it is blazing fast and comes with super useful features during development.


## Requirements

In the last part of the guide we have prepared the development environment with ddev and create the initial directory structure. It is therefore required that you have these in place.


## Setup Vite with DDEV

Vite is a modern build tool that fascilitates the integration of front-end libraries also for development. You can have a more general introduction to using DDEV with Vite [here](https://ddev.com/blog/working-with-vite-in-ddev/). It is easy to setup ddev with vite by using an add-on called [ddev-vite-sitecar](https://github.com/s2b/ddev-vite-sidecar). The Vite development server is exposed as a https://vite.* subdomain to your project’s main domain, which means that no ports need to be exposed to the host system. 

### Add Vite support to ddev

Install the add-on for ddev:

```bash
ddev add-on get s2b/ddev-vite-sidecar
```

Then restart your project:

```bash
ddev restart
```

### Vite installation and configuration

Next we will need to install Vite in our project and configure it so that DDEV can use it.

Install vite with ddev:
```bash
ddev npm i vite --save-dev
```

Configure Vite in our framework's project root at `vite.config.js` (read the comments for the meaning):
```js
import { defineConfig } from "vite"
import path from "path"

// https://vitejs.dev/config/
export default defineConfig({
  // Add entrypoint
  build: {
    // Defines the input files to be watched and build
    rollupOptions: {
      input: path.resolve(__dirname, "resources/js/app.js"),
    },

    // manifest
    manifest: true,

    // Defines the destination directory of the generated assets
    outDir: './build',
  },

  // Adjust Vite's dev server for DDEV
  // https://vitejs.dev/config/server-options.html
  server: {
    // Respond to all network requests
    host: "0.0.0.0",
    strictPort: true,
    // Defines the origin of the generated asset URLs during development, this must be set to the
    // Vite dev server URL given by ddev-vite-sitecar add-on variable VITE_SERVER_URI.
    origin: process.env.VITE_SERVER_URI,
    // Configure CORS securely for the Vite dev server to allow requests from *.ddev.site domains,
    // supports additional hostnames (via regex). If you use another `project_tld`, adjust this.
    cors: {
      origin: /https?:\/\/([A-Za-z0-9\-\.]+)?(\.ddev\.site)(?::\d+)?$/,
    },
  },
})
```
Add the following scripts to your `package.json` for being able to easily start vite from ddev.

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
```

We can now launch Vite with `ddev vite` or `ddev npm run dev` which will both spin up the Vite Development Server that is being server from ddev under its own Subdomain (it is stored in the variable VITE_SERVER_URI). This means that during development our assets will be served from the development server, and after build from the output directory `/build`. 

### Vite PHP Backend Integration

Before we start with the backend integration, let's first add a style file so we can test it. Add a new file `resources/css/app.css`:

```css
h1 {
    font-size: 32px;
    font-weight: bold;
    text-decoration: underline;
}
```

Next, we will add a JavaScript file, that will import our Stylesheet (this is required for Vite to handle some internal features) and also log something to console, so we can see that also JavaScript is working. Add a new file `resources/js/app.js`:

```js
import 'vite/modulepreload-polyfill'
import '../css/app.css'

console.log("Hello World");
```

Now, start Vite in development mode:

```bash
ddev vite
```

#### Example Integration

To reference our styles in our website, simply replace `pages/home.php` with following:

```html
<!doctype html>
<html>
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- This is just an example for local development, no full integration: -->
    <script type="module" src="<?php echo getenv('VITE_SERVER_URI'); ?>/@vite/client"></script>
    <script type="module" src="<?php echo getenv('VITE_SERVER_URI'); ?>/resources/js/app.js"></script>
    <!-- see https://vitejs.dev/guide/backend-integration.html -->
    </head>
    <body>
    <h1>
        Hello world
    </h1>
    </body>
</html>
```

`getenv('VITE_SERVER_URI')` will insert the URI to the Vite Development Server, where we will include the Vite Client and our `app.js`. Through this, styles will be imported with fill Hot Module Reloading support.

#### Full Integration

If we would like to integrate this fully, it would mean that we have to write a PHP logic that inserts the development URI when jn development and else parse the latest build tag from the manifest and reference that build. Fortunately, there is a ready-made package [php-vite](https://github.com/mindplay-dk/php-vite) that will take over that task for us.

Install php-vite through composer:

```bash
ddev composer require mindplay/php-vite
```

Load php-vite and configure to be used in `bootstrap/app.php`:

```php
<?php

use mindplay\vite\Manifest;

const BUILD_PATH = __DIR__ . "/../build";

$dev = getenv('APP_ENV') !== 'production';

if($dev === true) {
    $base_path = getenv('VITE_SERVER_URI') . "/";
    $manifest_path = '';

} else {
    $base_path = BUILD_PATH;
    $manifest_path = BUILD_PATH . '/.vite/manifest.json';
}

$vite = new Manifest(
    manifest_path: $manifest_path,
    base_path: $base_path,
    dev: $dev
);

$tags = $vite->createTags(__DIR__ . "/../resources/js/app.js");

include_once __DIR__. "/../pages/home.php";
```

Add the created tags in `pages/home.php`:
```php
<!doctype html>
<html>
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <?= $tags->css ?>
    </head>
    <body>
    <h1 class="text-3xl font-bold underline">
        Hello world
    </h1>
    <?= $tags->js ?>
    </body>
</html>
```

## Integrating CSS Library "Tailwind"

## Integrating JavaScript Library "Svelte"

