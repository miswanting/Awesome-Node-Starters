# TypeScript-Electron-Vue-Webpack-Starter
A manual starter for minimalists &amp; fundmentalists.

>Basic:
>
>- [x] Typescript
>- [x] Electron
>- [x] Vue
>- [x] Webpack
>
>Components(Optional):
>
>- [x] Simple Components
>- [x] Single File Components(SFCs)
>  - [x] Using decorators

## Index

[TOC]

[TypeScript-Electron-Vue-Webpack-Starter](#typescript-electron-vue-webpack-starter)

​	[Index](#index)

​	[Basic Configuration](#basic-configuration)

​		[Initialize your project](#initialize-your-project)

​		[Install dependencies](#install-dependencies)

​		[Add a TypeScript configuration file](#add-a-typescript-configuration-file)

​		[Adding a Webpack configuration file](#adding-a-webpack-configuration-file)

​		[Add build & debug scripts](#add-build--debug-scripts)

​		[Create a basic project](#create-a-basic-project)

​	[Component Style(Optional)](#component-styleoptional)

​		[Simple Components](#simple-components)

​		[Single File Components(SFCs)](#single-file-componentssfcs)

​			[Using decorators](#using-decorators)

## Basic Configuration

### Initialize your project

```bash
mkdir TypeScript-Electron-Vue-Webpack-Starter
cd TypeScript-Electron-Vue-Webpack-Starter
git init
mkdir src
cd src
mkdir components
cd ..
npm init
```

### Install dependencies

```bash
npm i -D typescript electron webpack webpack-cli ts-loader css-loader vue vue-loader vue-template-compiler
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
mainConfig = {
    entry: './src/Main.ts',
    mode: 'development',
    target: 'electron-main',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'Main.js'
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'ts-loader',
                exclude: /node_modules/
            },
        ]
    },
    resolve: {
        extensions: ['.ts', '.js', '.json'],
    }
}
rendererConfig = {
    entry: './src/Renderer.ts',
    mode: 'development',
    target: 'electron-renderer',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'Renderer.js'
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
            }
        ]
    },
    resolve: {
        extensions: ['.ts', '.js', '.vue', '.json'],
        alias: {
            'vue$': 'vue/dist/vue.esm.js'
        }
    }
}
module.exports = [mainConfig, rendererConfig]
```

### Add build & debug scripts

package.json

```json
{
    "main": "dist/Main.js",
    "scripts": {
        "build": "webpack",
        "debug": "npx electron .",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
}
```

### Create a basic project

src/Index.html

```html
<!doctype html>
<html>

<head></head>

<body>
    <div id="app"></div>
</body>
<script src="../dist/Renderer.js"></script>

</html>
```

src/Main.ts

```typescript
import { app, BrowserWindow } from "electron";

let mainWindow: Electron.BrowserWindow;

function createWindow() {
    // Create the browser window.
    mainWindow = new BrowserWindow({
        height: 600,
        width: 800,
    });

    // and load the index.html of the app.
    mainWindow.loadFile("./src/Index.html");

    // Open the DevTools.
    mainWindow.webContents.openDevTools();

    // Emitted when the window is closed.
    mainWindow.on("closed", () => {
        // Dereference the window object, usually you would store windows
        // in an array if your app supports multi windows, this is the time
        // when you should delete the corresponding element.
        // mainWindow = null;
        mainWindow.destroy();
    });
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on("ready", createWindow);

// Quit when all windows are closed.
app.on("window-all-closed", () => {
    // On OS X it is common for applications and their menu bar
    // to stay active until the user quits explicitly with Cmd + Q
    if (process.platform !== "darwin") {
        app.quit();
    }
});

app.on("activate", () => {
    // On OS X it"s common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (mainWindow === null) {
        createWindow();
    }
});

// In this file you can include the rest of your app"s specific main process
// code. You can also put them in separate files and require them here.
```

src/Renderer.ts

```typescript
import Vue from "vue";

let v = new Vue({
    el: "#app",
    template: `
    <div>
        <div>Hello {{name}}!</div>
        Name: <input v-model="name" type="text">
    </div>`,
    data: {
        name: "World"
    }
});
```

## Component Style(Optional)

### Simple Components

src/Renderer.ts

```typescript
import Vue from "vue";
import HelloComponent from "./components/Hello";

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

src/components/Hello.ts

```typescript
import Vue from "vue";

export default Vue.extend({
    template: `
        <div>
            <div>Hello {{name}}{{exclamationMarks}}</div>
            <button @click="decrement">-</button>
            <button @click="increment">+</button>
        </div>
    `,
    props: ['name', 'initialEnthusiasm'],
    data() {
        return {
            enthusiasm: this.initialEnthusiasm,
        }
    },
    methods: {
        increment() { this.enthusiasm++; },
        decrement() {
            if (this.enthusiasm > 1) {
                this.enthusiasm--;
            }
        },
    },
    computed: {
        exclamationMarks(): string {
            return Array(this.enthusiasm + 1).join('!');
        }
    }
});
```

### Single File Components(SFCs)

webpack.config.js

```javascript
const path = require('path')
const VueLoaderPlugin = require('vue-loader/lib/plugin')
mainConfig = {
    entry: './src/Main.ts',
    mode: 'development',
    target: 'electron-main',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'Main.js'
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'ts-loader',
                exclude: /node_modules/
            },
        ]
    },
    resolve: {
        extensions: ['.ts', '.js', '.json'],
    }
}
rendererConfig = {
    entry: './src/Renderer.ts',
    mode: 'development',
    target: 'electron-renderer',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'Renderer.js'
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
module.exports = [mainConfig, rendererConfig]
```

src/Renderer.ts

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
<template>
    <div>
        <div class="greeting">Hello {{name}}{{exclamationMarks}}</div>
        <button @click="decrement">-</button>
        <button @click="increment">+</button>
    </div>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
    props: ['name', 'initialEnthusiasm'],
    data() {
        return {
            enthusiasm: this.initialEnthusiasm,
        }
    },
    methods: {
        increment() { this.enthusiasm++; },
        decrement() {
            if (this.enthusiasm > 1) {
                this.enthusiasm--;
            }
        },
    },
    computed: {
        exclamationMarks(): string {
            return Array(this.enthusiasm + 1).join('!');
        }
    }
});
</script>

<style>
.greeting {
    font-size: 20px;
}
</style>
```

#### Using decorators

```bash
npm i -D vue-class-component vue-property-decorator
```

tsconfig.json

```json
{
    "compilerOptions": {
        "experimentalDecorators": true
    }
}
```

src/components/Hello.vue

```vue
<template>
  <div>
    <div class="greeting">Hello {{name}}{{exclamationMarks}}</div>
    <button @click="decrement">-</button>
    <button @click="increment">+</button>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from "vue-property-decorator";

@Component
export default class HelloDecorator extends Vue {
  @Prop() name!: string;
  @Prop() initialEnthusiasm!: number;

  enthusiasm = this.initialEnthusiasm;

  increment() {
    this.enthusiasm++;
  }
  decrement() {
    if (this.enthusiasm > 1) {
      this.enthusiasm--;
    }
  }

  get exclamationMarks(): string {
    return Array(this.enthusiasm + 1).join("!");
  }
}
</script>

<style>
.greeting {
  font-size: 20px;
}
</style>
```
