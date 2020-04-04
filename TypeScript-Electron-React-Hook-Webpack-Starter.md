# TypeScript-Electron-React-Hook-Webpack-Starter
A manual starter for minimalists &amp; fundmentalists.

>Basic:
>
>- [x] Typescript
>- [x] Electron
>- [x] React Hook
>- [x] Webpack
>

## Index

[TOC]

## Basic Configuration

### Initialize your project

```bash
mkdir TypeScript-Electron-React-Hook-Webpack-Starter
cd TypeScript-Electron-React-Hook-Webpack-Starter
git init
mkdir src
cd src
mkdir components
cd ..
npm init
```

### Install dependencies

```bash
npm i -D typescript electron webpack webpack-cli react react-dom @types/react @types/react-dom ts-loader source-map-loader
```

### Add a TypeScript configuration file

tsconfig.json

```json
{
    "compilerOptions": {
        "outDir": "./dist/",
        "sourceMap": true,
        "noImplicitAny": true,
        "module": "commonjs",
        "target": "es5",
        "jsx": "react",
        "esModuleInterop": true
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
    mode: 'development',
    target: 'electron-main',
    entry: './src/main.ts',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'main.js'
    },
    devtool: "source-map",
    module: {
        rules: [
            {
                test: /\.ts$/,
                loader: "awesome-typescript-loader"
            }, {
                enforce: "pre",
                test: /\.js$/,
                loader: "source-map-loader"
            }
        ]
    },
    resolve: {
        extensions: [".ts", ".js", ".json"],
    }
}
rendererConfig = {
    mode: 'development',
    target: 'electron-renderer',
    entry: './src/renderer.tsx',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'renderer.js'
    },
    devtool: "source-map",
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: "awesome-typescript-loader"
            }, {
                enforce: "pre",
                test: /\.js$/,
                loader: "source-map-loader"
            }
        ]
    },
    resolve: {
        extensions: ['.ts', '.tsx', '.js', '.jsx', '.json']
    }
}
module.exports = [mainConfig, rendererConfig]
```

### Add build & debug scripts

package.json

```json
{
    "main": "dist/main.js",
    "scripts": {
        "build": "webpack",
        "debug": "npx electron ."
    },
}
```

### Create a basic project

src/index.html

```html
<!doctype html>
<html>

<head></head>

<body>
    <div id="root"></div>
</body>
<script src="../dist/renderer.js"></script>

</html>
```

src/main.ts

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
    mainWindow.loadFile("./src/index.html");

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

src/renderer.tsx

```typescript
import React from "react";
import ReactDOM from "react-dom";

import { Hello } from "./components/Hello";

ReactDOM.render(
    <Hello />,
    document.getElementById("root")
);
```

src/components/Hello.tsx

```tsx
import React, { useState } from "react";

export function Hello() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={() => setCount(count + 1)}>
                Click me
        </button>
        </div>
    );
}
```

