# TypeScript-Webpack-React-Hook-Starter

A manual starter for minimalists &amp; fundmentalists.

> Basic:
>
> - [x] Typescript
> - [x] React Hook
> - [x] Webpack
> 

## Index

[TOC]

## Basic Configuration

### Initialize your project

```bash
mkdir TypeScript-Webpack-React-Hook-Starter
cd TypeScript-Webpack-React-Hook-Starter
git init
mkdir src
cd src
mkdir components
cd ..
npm init
```

### Install dependencies

```bash
npm i -D webpack webpack-cli react react-dom @types/react @types/react-dom typescript awesome-typescript-loader source-map-loader
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
        "target": "ESNext",
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
module.exports = {
    entry: "./src/index.tsx",
    mode: "development",
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'index.js'
    },

    // Enable sourcemaps for debugging webpack's output.
    devtool: "source-map",

    resolve: {
        // Add '.ts' and '.tsx' as resolvable extensions.
        extensions: [".ts", ".tsx", ".js", ".json"]
    },

    module: {
        rules: [
            // All files with a '.ts' or '.tsx' extension will be handled by 'awesome-typescript-loader'.
            { test: /\.tsx?$/, loader: "awesome-typescript-loader" },

            // All output '.js' files will have any sourcemaps re-processed by 'source-map-loader'.
            { enforce: "pre", test: /\.js$/, loader: "source-map-loader" }
        ]
    },

    // When importing a module whose path matches one of the following, just
    // assume a corresponding global variable exists and use that instead.
    // This is important because it allows us to avoid bundling all of our
    // dependencies, which allows browsers to cache those libraries between builds.
    // externals: {
    //     "react": "React",
    //     "react-dom": "ReactDOM"
    // }
};
```

### Add build & debug scripts

package.json

```json
{
    "main": "dist/index.js",
    "scripts": {
        "build": "webpack",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
}
```

## Create a basic project

src/index.html

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
</head>

<body>
    <div id="root"></div>
    <!-- Main -->
    <script src="../dist/index.js"></script>
</body>

</html>
```

src/index.tsx

```tsx
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

