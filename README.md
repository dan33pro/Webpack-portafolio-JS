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