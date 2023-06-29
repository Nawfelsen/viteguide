# Vite

<center>
<img src="./assets/vite-logo.jpeg" alt="vite logo" width="300" margin-left="500"/>
</center>

## Introduction

Hello ! Welcome to the Vite migration guide ! 

Created by **Evan You**, **Vite** is the recommended CLI for your Vue project. This guide will help you to migrate from Vue CLI to Vite. I will try my best to help you thanks to the experience I gain during the **Partner** migration.

> **Good news:** Vite can run with Vue2 ! I will tell you the difference between Vite+Vue2 and Vite+Vue3. So don't worry about that.

If you encountered any other issues during your migration, feel free to update the guide with your different troubleshooting.

> **Warning:** Vite only support moderns browsers (Chrome, Mozilla, etc...)

Ready ? Let's Go !

## Table of Contents

- [Vite](#vite)
  - [Introduction](#introduction)
  - [Table of Contents](#table-of-contents)
  - [How to configure Vite](#how-to-run-the-project-with-vite)
    - [Step 1: Update Dependencies](#step-1-update-dependencies)
    - [Step 2: Add Vite Config File](#step-2-add-vite-config-file)
    - [Step 3: Moving the index.html](#step-3-moving-the-indexhtml)
    - [Step 4: Update the Scripts](#step-4-update-the-scripts)
    - [Step 5: Last Adjustments](#step-5-last-adjustments)
    - [Step 6: Enjoy Vite](#step-6-enjoy-vite)
  - [Troubleshooting](#troubleshooting)
  - [Resources](#resources)

## How to configure Vite

### Step 1: Update Dependencies 

Firstly, we need to remove all @vue/cli related dependencies:

```json
// package.json

"@vue/cli-plugin-babel": "~4.5.0", // remove
"@vue/cli-plugin-eslint": "~4.5.0", // remove
"@vue/cli-plugin-router": "~4.5.0", // remove
"@vue/cli-plugin-vuex": "~4.5.0", // remove
"@vue/cli-service": "~4.5.0", // remove
```

> **Note:** If you are using @vue/cli v1.x, there's none visible @vue/cli dependencies in your package.json

The **sass-loader** and **node-sass** dependencies are no longer useful because Vite provides built in support, so we can remove them:

```json
// package.json

"sass-loader": "^8.0.2" // remove
"node-sass": "^4.14.1" // remove
```

> **Warning:** Sometimes these dependencies are useful for your unit test

Instead add the **sass** dependencies:

```json
// package.json

"sass": "^1.55.0"
```

Remove every **webpack** config file and webpack dependencies:

```json
// package.json

"webpack": "^2.7.0", // remove
"webpack-merge": "^4.2.1" // remove
```

Finally, add **Vite** dependencies:

```json
// package.json

  "devDependencies": {
    ...
    "@vitejs/plugin-vue": "^3.2.0", // only for vue 3
    "@vitejs/plugin-vue2": "^2.0.0", // only for Vue 2
    "vite": "^4.0.1",
    ...
  }
```

### Step 2: Vite Config File 

To configure **Vite**, create a file named "**vite.config.js**" at the root directory:

```js
// vite.config.js

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue2' // for vue 2
import vue from '@vitejs/plugin-vue' // for vue 3

const path = require('path')

export default defineConfig({
    plugins: [vue({
        template: {
            compilerOptions: {
                whitespace: 'preserve'
            }
        }
    })],
    server: {
        port: 8092
    },
    preview: {
        port: 8092,
    },
    resolve: {
        extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue'],
        alias: {
            '@': path.resolve(__dirname, './src'),
            stream: 'stream-browserify',
            vue: 'vue/dist/vue.js'
        },
    },
    build: {
        chunkSizeWarningLimit: 1500,
        rollupOptions: {
            output: {
                // eslint-disable-next-line consistent-return
                manualChunks(id) {
                    if (id.includes('node_modules')) {
                        return id.toString().split('node_modules/')[1].split('/')[0].toString()
                    }
                }
            }
        }
    }
})
```

> **Warning:** Be sure you import the correct plugin according to your Vue version

The '.vue' extension is now necessary

From:

```js
import HelloWorld from "@/components/HelloWorld";
```

To:

```js
import HelloWorld from "@/components/HelloWorld.vue";
```

### Step 3: Moving the index.html

**index.html** has to be at the **root directory**. So make sure it's at the right place.

In the index.html file, we are no longer able to use the following syntax:

```html
<!--index.html-->

<!--remove-->
<title><%= htmlWebpackPlugin.options.title %></title> 
```

Instead, use **hard code** like this:

```html
<!--index.html-->

<!--add-->
<title>Hard Coded Title</title>
```

> **Note:** title tag is just an example, apply the same change for others tags

Finally, we need to import main.js at the end of the body:

```html
<!--index.html-->

<script type="module" src="/src/main.js"></script>
```

### Step 4: Update scripts section

Now, we are going to adapt the scripts section in **package.json** to fit with **Vite**. So remove your **dev**, **build** and **serve** scripts and add the following scripts:

```json
// package.json

"dev": "vite",
"build": "vite build",
"serve": "vite preview",
```

> **Note:** For dev, build and preview configuration, configure it in vite.config.js

### Step 5: Last Adjustments

In each file, delete "lang=html" on your template.

And finally, require is not recognized anymore so you need to replace require by import

> **Note:** If your project use eslint, update it to the latest version

### Step 6: Enjoy Vite

Now, we can run:

```shell
npm install && npm run dev
```

And enjoy the benefits of **Vite** !

## Troubleshooting

### Some pages doesn't work

If some pages doesn't work correctly, it can be because your vue-router version is outdated. So be sure to install the latest vue-router version available:

```json
"vue-router": "3.0.0", // for vue 2
"vue-router": "4.0.0", // for vue 3
```

> **Note:** During the migration of Partner, Managed Security page doesn't work and the update of vue-router resolve the issue

### CI issues

If you don't have the correct version of Node, Vite can't run correctly. So, be sure to install the [correct version of Node](https://github.com/nodejs/release#release-schedule)

> **Note:** Vite support node 14.18+ 

### Space issues

Vite can break your space. To avoid this you can add the following option to the plugin:

```js
// vite.config.js
export default defineConfig({
  ...
  plugins: [vue({
        template: {
            compilerOptions: {
                whitespace: 'preserve'
            }
        }
  })],
  ...
```

### lang=html

In each file, delete "lang=html" on your template tag

From:

```html
<template lang="html">
```

To:

```html
<template>
```
### Require to Import

Require is not recognized anymore so you need to replace require by import

## Recommandation

If your project use eslint, update it to the latest version

More information on [Eslint](https://eslint.org/)

## Resources

- More information on [Vue.js](https://vuejs.org/)
- More information on [Vite](https://vitejs.dev/)