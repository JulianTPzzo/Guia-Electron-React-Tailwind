
<h1 align="center">Inicializar proyecto Electron React Tailwind</h1>

Template de Electron con webpack

```sh
npm init electron-app@latest name-app -- --template=webpack
```

Instalar babel-loader
```sh
npm install --save-dev @babel/core @babel/preset-react babel-loader
```
Agregar ReactJS a tu proyecto
```sh
npm install --save react react-dom
```
Crear main.jsx y configurarlo
```sh
import * as React from 'react';
import { createRoot } from 'react-dom/client';
import App from './view/App.jsx';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```
Crear componente App.jsx
```sh
import React from 'react';

const App = () => {
  return (
    <div className='font-normal text-5xl'>xDD</div>
  );
};

export default App;

```
Configurar archivos de Electron Webpack

renderer.js
```
import './main.jsx';
```
preload.js
```sh
window.ipcRenderer = require('electron').ipcRenderer;
```
webpack.rules.js
```sh
module.exports = [
  {
    test: /native_modules[/\\].+\.node$/,
    use: 'node-loader',
  },
  {
    test: /\.jsx?$/,
    use: {
      loader: 'babel-loader',
      options: {
        exclude: /node_modules/,
        presets: ['@babel/preset-react'],
      },
    },
  },
];
```
forge.config.js se debe ver de la siguiente forma:
```sh
const { FusesPlugin } = require('@electron-forge/plugin-fuses');
const { FuseV1Options, FuseVersion } = require('@electron/fuses');

module.exports = {
  packagerConfig: {
    asar: true,
  },
  rebuildConfig: {},
  makers: [
    {
      name: '@electron-forge/maker-squirrel',
      config: {},
    },
    {
      name: '@electron-forge/maker-zip',
      platforms: ['darwin'],
    },
    {
      name: '@electron-forge/maker-deb',
      config: {},
    },
    {
      name: '@electron-forge/maker-rpm',
      config: {},
    },
  ],
  plugins: [
    {
      name: '@electron-forge/plugin-auto-unpack-natives',
      config: {},
    },
    {
      name: '@electron-forge/plugin-webpack',
      config: {
        mainConfig: './webpack.main.config.js',
        renderer: {
          config: './webpack.renderer.config.js',
          entryPoints: [
            {
              html: './src/index.html',
              js: './src/renderer.js',
              name: 'main_window',
              preload: {
                js: './src/preload.js',
              },
            },
          ],
        },
      },
    },
    // Fuses are used to enable/disable various Electron functionality
    // at package time, before code signing the application
    new FusesPlugin({
      version: FuseVersion.V1,
      [FuseV1Options.RunAsNode]: false,
      [FuseV1Options.EnableCookieEncryption]: true,
      [FuseV1Options.EnableNodeOptionsEnvironmentVariable]: false,
      [FuseV1Options.EnableNodeCliInspectArguments]: false,
      [FuseV1Options.EnableEmbeddedAsarIntegrityValidation]: true,
      [FuseV1Options.OnlyLoadAppFromAsar]: true,
    }),
  ],
};

```
Instalar dependencias de Tailwind
```sh
npm install --save-dev css-loader style-loader postcss-loader tailwindcss autoprefixer
```
Crear archivo de configuración de Tailwind CSS y de PostCSS
```sh
npx tailwindcss init -p
```
postcss.config.js
```sh
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
  ],
};
```
webpack.renderer.config.js
```sh
const rules = require('./webpack.rules');

rules.push({
  test: /\.css$/,
  use: [
    { loader: 'style-loader' },
    { loader: 'css-loader' },
    {
      loader: 'postcss-loader',
      options: {
        postcssOptions: {
          plugins: [
            require('tailwindcss'),
            require('autoprefixer'),
          ],
        },
      },
    },
  ],
});

module.exports = {
  target: 'electron-renderer',
  module: {
    rules,
  },
};
```
En main.js
```sh
webPreferences: {
  preload: MAIN_WINDOW_PRELOAD_WEBPACK_ENTRY,
  nodeIntegration: true,
  contextIsolation: false,
},
```
tailwind.config.js
```sh
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```
Archivo CSS principal
```sh
@tailwind base;
@tailwind components;
@tailwind utilities;
```
