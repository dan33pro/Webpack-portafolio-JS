# Webpack-portafolio-JS
Este es el proyecto que trabajamos en el curso de Webpack, que es
profesor Oscar Barajas [repoOriginal](https://github.com/gndx/js-portfolio)

## Configuración del proyecto

Lo primero que se hizo fue organizar la estructura base del
proyecto, luego se inicializo el control de versiones de Git, 
también, usamos `npm init`, `npm install webpack wepack-cli -D`,
`npx webpack --mode production`.

Ahora vamos a crear un archivo de configuración de Webpack, para esto
creamos un archivo en la raíz del proyecto con nombre

`webpack.config.js`

Primero lo preparamos para saber cual es el punto de entrada, hacía donde
va a enviar la compilación del proyecto y cuales son las extensiones que
va a estar trabajando.

1. Importamos 'path' que ya viene disponible en Node

```javascript
const path = require('path');
```

Ahora creamos un objeto para exportar con la configuración deseada:
1. Punto de entrada: `entry`
2. Hacía donde va enviar la compilación : output
    - path: que es la ruta, y lo hacemos con resolve() a fin de evitar
    problemas de enrutamiento, así siempre va a encontrar la ruta, incluso
    si esta en la nube.
    - filename: nombre del archivo de salida
3. Luego las extenciones con las que vamos a trabajar

```javascript
module.export = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'main.js',
    },
    resolve: {
        extensions: ['.js']
    }
}
```

Con esto ya podemos preparar el proyecto con Webpack con:

```npx
npx webpack --mode production --config webpack.config.js
```

Y para facilitarnos las cosas añadimos en el `package.json`
el siguiente script, ejecutamos con `npm run build`

```
"build": "webpack --mode production"
```

## Babel Loader para JavaScript

Babel nos permite hacer que nuestro código sea compatible con
todos los navegadores, para agregarlo al proyecto debemos agregar
las siguientes dependencias.

```npm
npm install babel-loader @babel/core @babel/preset-env @babel/plugin-transform-runtime -D
```

El plugin nos permite trabajar con asincronismo, ahora tenemos que pasarle
la configuración a nuestro proyecto de Babel, para esto creamos un archivo
con nombre `.babelrc` donde vamos a añadir:

```
{
    "presets": [
        "@babel/preset-env"
    ],
    "plugins": [
        "@babel/plugin-transform-runtime"
    ]
}
```

Podemos tener N presets y plugins, en este caso el preset-env
es para trabajar con JavaScript moderno, y el plugin ya lo mencionamos
es para que tenga atención con el asincronismo.

Ahora volvemos a nuestro archivo [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) y agregamos debajo de resolve

```
module: {
        rules: [
            {
                test: /\.m?js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader'
                }
            }
        ]
    },
```

Este module es un objeto que tiene un arreglo de rules, dentro
del arreglo añadimos un objeto con propiedad `test:` que sive para
decirle que sean todos los archivos que terminen con `.mjs` o `.js`, 
con `exclude:` le decimos que ignore todos los que esten el `node_modules`
y le decimos con `use:` que use como `loader` `babel-loader`.

Volvemos a compilar a producción con el srcipt `build`

````npm
npm run build
```