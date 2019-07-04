# TypeScript-Vue-Webpack-Pug-Sass-Starter
A manual starter for minimalists &amp; fundmentalists.

>Basic:
>
>- [x] Typescript
>- [x] Electron
>- [x] Vue
>- [x] Webpack
>- [x] Single File Components(SFCs)
>  - [x] Using decorators
>- [x] Pug
>- [x] Sass

## Index

[TOC]

- [TypeScript-Electron-Vue-Webpack-Starter](#typescript-electron-vue-webpack-starter)
  - [Index](#index)
  - [Basic Configuration](#basic-configuration)
    - [Initialize your project](#initialize-your-project)
    - [Install dependencies](#install-dependencies)
    - [Add a TypeScript configuration file](#add-a-typescript-configuration-file)
    - [Adding a Webpack configuration file](#adding-a-webpack-configuration-file)
    - [Add build & debug scripts](#add-build--debug-scripts)
    - [Create a basic project](#create-a-basic-project)
  - [Component Style(Optional)](#component-styleoptional)
    - [Simple Components](#simple-components)
    - [Single File Components(SFCs)](#single-file-componentssfcs)
      - [Using decorators](#using-decorators)

## Basic Configuration

### Initialize your project

```bash
mkdir TypeScript-Vue-Webpack-Pug-Sass-Starter
cd TypeScript-Vue-Webpack-Pug-Sass-Starter
git init
mkdir src
cd src
mkdir components
cd ..
npm init
```

### Install dependencies

```bash
npm i -D typescript electron webpack webpack-cli ts-loader css-loader vue vue-loader vue-template-compiler pug pug-plain-loader sass-loader node-sass
```

### Add a TypeScript configuration file

tsconfig.json

```json
{
    "compilerOptions": {
        "outDir": "./dist/",
        "sourceMap": true,
        "strict": true,
        "noImplicitAny":true,
        "noImplicitReturns": true,
        "module": "ES6",
        "moduleResolution": "Node",
        "target": "ES5"
    },
    "include": [
        "./src/**/*"
    ]
}
```

### Adding a Webpack configuration file

webpack.config.js

```js
const path = require('path')
const VueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
    entry: './src/Index.ts',
    mode: 'development',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'Index.js'
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'ts-loader',
                exclude: /node_modules/,
                options: {
                    appendTsSuffixTo: [/\.vue$/],
                }
            },
            {
                test: /\.vue$/,
                loader: 'vue-loader',
                options: {
                    loaders: {
                        // Since sass-loader (weirdly) has SCSS as its default parse mode, we map
                        // the "scss" and "sass" values for the lang attribute to the right configs here.
                        // other preprocessors should work out of the box, no loader config like this necessary.
                        'scss': 'vue-style-loader!css-loader!sass-loader',
                        'sass': 'vue-style-loader!css-loader!sass-loader?indentedSyntax',
                    }
                    // other vue-loader options go here
                }
            },
            {
                test: /\.css$/,
                use: [
                    'vue-style-loader',
                    'css-loader'
                ]
            },
            {
                test: /\.pug$/,
                loader: 'pug-plain-loader'
            }
        ]
    },
    resolve: {
        extensions: ['.ts', '.js', '.vue', '.json'],
        alias: {
            'vue$': 'vue/dist/vue.esm.js'
        }
    },
    plugins: [
        // 请确保引入这个插件！
        new VueLoaderPlugin()
    ]
}
```

### Add build & debug scripts

package.json

```json
{
    "main": "dist/Main.js",
    "scripts": {
        "build": "webpack",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
}
```

## Create a basic project

Index.html

```html
<!doctype html>
<html>

<head></head>

<body>
    <div id="app"></div>
</body>
<script src="./dist/Index.js"></script>

</html>
```

src/Index.ts

```typescript
import Vue from "vue";
import HelloComponent from "./components/Hello.vue";

let v = new Vue({
    el: "#app",
    template: `
    <div>
        Name: <input v-model="name" type="text">
        <hello-component :name="name" :initialEnthusiasm="5" />
    </div>
    `,
    data: { name: "World" },
    components: {
        HelloComponent
    }
});
```

src/vue-shims.d.ts

```typescript
declare module "*.vue" {
    import Vue from "vue";
    export default Vue;
}
```

src/components/Hello.vue

```vue
<template lang="pug">
    div
        div(class="greeting") Hello {{name}}{{exclamationMarks}}
        button(@click="decrement") -
        button(@click="increment") +
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  props: ["name", "initialEnthusiasm"],
  data() {
    return {
      enthusiasm: this.initialEnthusiasm
    };
  },
  methods: {
    increment() {
      this.enthusiasm++;
    },
    decrement() {
      if (this.enthusiasm > 1) {
        this.enthusiasm--;
      }
    }
  },
  computed: {
    exclamationMarks(): string {
      return Array(this.enthusiasm + 1).join("!");
    }
  }
});
</script>
```
