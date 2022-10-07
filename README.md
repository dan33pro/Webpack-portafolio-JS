# Webpack-portafolio-JS
Este es el proyecto que trabajamos en el curso de Webpack, que es
profesor Oscar Barajas [repoOriginal](https://github.com/gndx/js-portfolio)

## Configuración del proyecto

Lo primero que se hizo fue organizar la estructura base del
proyecto, luego se inicializo el control de versiones de Git, 
también, usamos `npm init -y`, `npm install webpack webpack-cli -D`,
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
module.exports = {
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

## Loaders y Plugins en Webpack

### Babel Loader para JavaScript

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

```npm
npm run build
```

### HTML en Webpack

Para trabajar con HTML y Webpack necesitamos instalar la siguiente
dependencia:

```npm
npm install html-webpack-plugin -D
```

Ahora agregamos la configuración del plugin a [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js), donde agregamos:

```javascript
const htmlWebpackPlugin = require('html-webpack-plugin');
```

Y añadimos debajo de `module` 

```javascript
plugins: [
        new htmlWebpackPlugin({
            inject: true,
            template: './public/index.html',
            filename: './index.html'
        })
    ]
```

Esto quiere decir `inject` inserción de los elementos, el `template`
es la ruta del `index.html` con el que estamos tranajando y `filename`
es el nombre del archivo resultante en el directorio `dist`.

Y removemos la linea `<script type="module" src="../src//index.js"></script>`
del archivo [index.html](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/public/index.html) ya que no la necesitamos.

Ahora ya podemos correr `npm run build` y ver el resultado

### Loaders para CCS

Comosiempre lo primero es agragar las depndencias de desarrollo que
necesitamos, css-loader para procesar archivos .css y un plugin para
poder unir css dividido en distintas partes de la aplicación, entonces
corremos el comando:

```npm
npm install mini-css-extract-plugin css-loader -D
```

Removemos `<link rel="stylesheet" href="../src/styles/main.css">` de nuestro template [inde.html](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/public/index.html) ya que Webpack se encargara de hacer
esta conexión.

Nos movemos al archivo [index.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/index.js) y removemos el `console.log('hola');` no lo necesitamos ya, lo siguiente es importar los estilos en este archivo, para esto usamos:

```javascript
import './styles/main.css';
```

Ahora agregamos la configuración necesaria de [webpack.config-js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) que necesitamos para que pueda trabajar con el loader que necesitamos, así que agregamos en el.

1. La connstante donde importamos el plugin que instalamos:
    ```javascript
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    ```
2.  La regla necesaria para reconocer archivos `.css` dentro del arreglo `rules` con el `loader` `css-loader` que también instalamos, sería la siguiente:
    ```javascript
    {
        test: /\.css$/i,
        use: [
                MiniCssExtractPlugin.loader,
                'css-loader'
            ],
        },
    ```

    Y ahora agregamos el plugin `mini-css-extract-plugin` dentro del arreglo `plugins` como una nueva instancia de nuestra constante, quedaría así:

    ```javascript
    plugins: [
        new HtmlWebpackPlugin({
            inject: true,
            template: './public/index.html',
            filename: './index.html'
        }),
        new MiniCssExtractPlugin(),
    ]
    ```

Con esto ya podemos probar el resultado con que es el siguiente script `"dev": "webpack --mode development"`  agregado en el [package.json](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/package.json) corriendo el comando `npm run dev`.

### Preprocesadores CSS (Stylus)

Comenzamos igual instalamos las dependencias necesarias:

```npm
npm install stylus stylus-loader -D
```

Como Stylus usa una extensión `.styl` hay que añadirlo al arreglo `rules` de [webpack.config-js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js), sencillamente modificamos la regla para `.css` existente, quedaría así:

```javascript
{
    test: /\.css|.styl$/i,
    use: [
        MiniCssExtractPlugin.loader,
        'css-loader',
        'stylus-loader'
    ],
},
```

Ya quedo la configuración de nuestro preprocesador de CSS, ahora a modo de prueba podemos crear un archivo `vars.styl` con lo que queramos dentro de `src/styles/`, por ejemplo:

```styl
$color-white = blue
$color-gray = #3F3D3D

body
    color $color-white
    background $color-gray
```

Y lo importamos en nuestro [index.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/index.js) con la linea:

```javascript
import './styles/vars.styl';
```

Ahora ya podemos correr el comando `npm run dev` y ver que es lo que pasa.

### Copia de archivos con Webpack

Instalamos el siguiente plugin:

```npm
npm install copy-webpack-plugin -D
```

Ahora vamos a ver que archivos debemos mover desde nuestra carpeta de `src` hacía la de `dist`, con esto modificamos nuestro [webpack.config-js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) y agregamos la constante para importar el plugin que recien instalamos:

```javascript
const CopyPlugin = require('copy-webpack-plugin');
```

Con esto agregamos lo siguiente al arreglo `plugins` del archivo como su siguiente elemento:

```javascript
new CopyPlugin({
    patterns: [ 
        {
            from: path.resolve(__dirname, "src", "assets/images"),
            to: "assets/images"
        } 
    ]
}),
```

Y ahora en nuestro [Template.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/templates/Template.js) que esta dentro de `src/templates` vamos a modificar todas las lineas donde traemos imagenes de `src="../src/assets/images/twitter.png"` a `src="assets/images/twitter.png"`, con esto ya podemos probar como simepre con el comando `npm run dev`.


### Loaders de imágenes

Para este indice vamos a hacer uso de `asset module` que nos provee Webpack así que no tendremos que añadir ningun loader, para esto tenemos que añadir una nueva regla en el array `rules` para trabajar con archivos `.png`, así que en nuestro archivo de [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js)  añadimos el siguiente objeto dentro del arreglo mencionado.

```javascript
{
    test: /\.png/,
    type: 'asset/resource'
},
```
En el indice anterior vimos como copiar archivos con Webpack, en este indice vamos a continuar mejorando este aspecto, así que en lugar de poner la ruta directamente en el `src=""` en su lugar podemos importar las imagenes y añadirlas en la propiedad que lo necesite, como una practica más sostenible.

Entonces importamos en el archivo [Template.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/templates/Template.js) los recursos con las siguientes lineas, es necesario el paso de `webpack.config.js` para poder hacer esto:

```javascript
import github from '../assets/images/github.png';
import twitter from '../assets/images/twitter.png';
import instagram from '../assets/images/instagram.png';
```

Y en cada atributo `src=""` agregamos la variable especifica ejemplo:

```javascript
<img src="${twitter}" />
```

Ahora probemolo con `npm run dev`, ya con esto notaremos que las imagenes estaran optimizadas detro de la carpeta `dist`.

### Loaders de fuentes

Una buena practica cuando integramos fuentes en nuestros proyectos es dejar de llamarlas desde sitios externos sino incorporarlas directamente en nuestro proyecto, para esto necesitamos:

1. Identificar las fuentes que estamos usando
    
    En nuestro caso en el archivo [main.css](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/styles/main.css) estamos importando la fuente siguiente `@import "https://fonts.googleapis.com/css?family=Ubuntu:300,400,500";`, por lo que no las estamos descargando en `.woff`, nosotros ya hemos
    recopilado una lista de fuentes para este proyecto, que estan en `src/assets/fonts` en el formato optimizado para la Web.

2. Ahora ya podemos remover la linea `@import "https://fonts.googleapis.com/css?family=Ubuntu:300,400,500";` en su lugar vamos a poner:

    ```css
    @font-face {
	    font-family: 'Ubuntu';
	    src: url('../assets/fonts/ubuntu-regular.woff2') format('woff2'),
	    	url('../assets/fonts/ubuntu-regular.woff') format('woff');
	    font-weight: 400;
	    font-style: normal;
    }
    ```

3. También necesitamos copiar las fuentes de `assets` a la carpeta `dist`, para esto necesitamos instalar unas dependencias que nos permiten leer archivos y moverlos, así que los instalamos con:

    ```npm
    npm install url-loader file-loader -D
    ```

    > Actualmente ya no es necesario e incluso puede ocasionar conflictos con Webpack 5

4. Nos movemos al archivo [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) y añadimos un nuevo elemento al arreglo `rules` con la siguiente configuración

    ```javascript
    {
        test: /\.(woff|woff2)$/,
        use: {
            loader: 'url-loader',
            options: {
                limit: 10000,
                mimetype: "application/font-woff",
                name: "[name].[ext]",
                outputPath: "./assets/fonts/",
                publicPath: "./assets/fonts/",
                esModule: false,
            },
        }
    }
    ```
    > Esta regla ya no es necesaria tal como esta arriba, revisar más abajo `Webpack 5 y conflictos`
    
    Y en la propiedad `output` del `module.exports` quedaría así

    ```javascript
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'main.js',
        assetModuleFilename: 'assets/images/[hash][ext][query]'
    },
    ```

    Nuevamente podemos compilar nuestro proyecto con `npm run dev`.

    > No me cargaba la fuente, así que decidi probar otra cosa
#### Webpack 5 y conflictos

Desde que salio Webpack 5 ya no es necesario instalar las dependencias de url-loader y file-loader.
En la versión 5 de webpack integraron las funciones que hacían esas 2 dependencias a los assets modules.

Esto me generaba conflictos en el resultado, en su lugar en el array `rules` añadimos lo siguiente

```javascript
{
    test: /\.woff|.woff2$/i,
    type: "asset/resource",
    generator: {
        filename: "assets/fonts/[name][ext]",
    },
},
```

Con esto se solucionaron mis problemas

### Optimización: hashes, compresión y minificación de archivos

Asignar un `hash` a cada archivo resulta importante para que nuestros usuarios no tengan problemas con la memoria cache una vez les entregamos una nueva versión, permitiendo que se carguen los recursos nuevos que remplazan a los antiguos.

1. Instalamos las dependencias necesarias

    ```npm
    npm install css-minimizer-webpack-plugin terser-webpack-plugin -D
    ```

2. Añadimos los recursos a nuestra configuración

    - Agregamos en [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) las constantes:

    ```javascript
    const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
    const TerserPlugin = require('terser-webpack-plugin');
    ```
    
    Y debajo de `plugins` agregamos

    ```javascript
    optimization: {
        minimize: true,
        minimizer: [
            new CssMinimizerPlugin(),
            new TerserPlugin(),
        ],
    }
    ```

    Con esto damos soporte de optimización para `css` y `Terser` para `JavaScript`

3. Otra cosa recomendable es modificar la propiedad `output` de `module.exports` por:

    ```javascript
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].[contenthash].js',
        assetModuleFilename: 'assets/images/[hash][ext][query]',
        clean: true,
    },
    ```

    Con esto nuestro `filename` ya usamos `hash`, para identificar cada `build`, ahora para las fuentes

    ```javascript
    {
        test: /\.woff|.woff2$/i,
        type: "asset/resource",
        generator: {
            filename: "assets/fonts/[name].[contenthash].[ext]",
        },
    },
    ```

    - En `plugins` podemos modificar `new MiniCssExtractPlugin()` por

    ```javascript
    new MiniCssExtractPlugin({
        filename: 'assets/[name].[contenthash].css',
    }),
    ```

4. Ahora compilamos el proyecto `npm run dev`

### Webpack Alias

Los alias nos sirven para facilitar la legibilidad de nuestros `paths` solucionando problemas como el de devolverse entre directorios que es inlegible, para usarlos, en [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) añadimos una nueva propiedad a `resolve` quedaría así

```javascript
resolve: {
    extensions: ['.js'],
    alias: {
        '@utils': path.resolve(__dirname, 'src/utils/'),
        '@templates': path.resolve(__dirname, 'src/templates/'),
        '@styles': path.resolve(__dirname, 'src/styles/'),
        '@images': path.resolve(__dirname, 'src/assets/images/'),
    }
},
```

Ya con estos `alias` podemos cambiar los `imports` de [index.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/index.js) y [Template.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/templates/Template.js), por estos respectivamente:

```javascript
import Template from '@templates/Template.js';
import '@styles/main.css';
import '@styles/vars.styl';
```

```javascript
import getData from '@utils/getData.js';
import github from '@images/github.png';
import twitter from '@images/twitter.png';
import instagram from '@images/instagram.png';
```

Y compilamos `npm run build`

## Deploy del proyecto

### Variables de Entorno

Es importante considerar las variables de entorno va 
a ser un espacio seguro donde podemos guardar datos sensibles.

1. Instalamos la dependencia necesaría con

    ```npm
    npm install dotenv-webpack -D
    ```
2. Añadimos la configuración necesaría

    - Creamos un archivo `.env` donde van a estar las variables de entorno

    > Este archivo no se sube al repositorio, y es secreto, el unico que
    va a ser publico es un archivo llamado `.env-example`, que también
    crearemos ahora.

3. Nos vamos al archivo [webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) y agregamos:

    ```javascript
    const Dotenv = require('dotenv-webpack');
    ```

    También en el arreglo de `plugins` vamos a introducri

    ```javascript
    new Dotenv(),
    ```

4. Ya configurada la dependencia, vamos al archivo [getData.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/src/utils/getData.js) que esta dentro de `src/utils/`.

    - Reemplazamos la linea

    ```javascript
    const API = 'https://randomuser.me/api/';
    ```

    Por

    ```javascript
    const API = process.env.API;
    ```

5. Por ultimo compilamos `npm run build`

### Webpack en modo desarrollo

1. Vamos a crear un nuevo archivo de configuración para separar las de
desarrollo de las de producción, el archivo lleva el nombre de
`webpack.config.dev.js`, en el copiamos todo lo de nuestro archivo
[webpack.config.js](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/webpack.config.js) pero removemos las siguientes lineas:

    ```javascript
    const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
    const TerserPlugin = require('terser-webpack-plugin');
    ```

    ```javascript
    optimization: {
        minimize: true,
        minimizer: [
            new CssMinimizerPlugin(),
            new TerserPlugin(),
        ]
    }
    ```

    Esto porque, en el modo desarrollo no nos intereza optimizar ni el `css`, ni el `JavaScript`, también añadimos la siguiente propiedad al `module.exports` 

    ```javascript
    mode: 'development',
    ```

2. Ahora nos movemos a nuestro archivo [package.json](https://github.com/dan33pro/Webpack-portafolio-JS/blob/main/package.json) y reemplazamos el `script` : 

    ```json
    "dev": "webpack --mode development"
    ``` 

    Por 

    ```json
    "dev": "webpack --config webpack.config.dev.js"
    ```
3. compilamos

    ```npm
    npm run dev
    ```