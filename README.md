# Sigoo Microfrontend Root Template

Contenedor construido en [Single SPA](https://single-spa.js.org/) para las aplicaciones de la suite **CM Sigoo**.

Proyecto propuesto para construir aplicaciones con arquitectura [Microfrontend](https://micro-frontends.org/).

## Para Empezar

### Prerrequisitos

Para iniciar la aplicación se recomienda tener instalado el CLI de ***[create-single-spa](https://single-spa.js.org/docs/create-single-spa)*** para crear la aplicación inicial y configurar su funcionalidad.

Se recomienda además usar Yarn en una versión >=3 para el manejo de paquetes y NodeJS 18 para inicializar este contenedor y sus aplicaciones.

## Instalación

Se necesitan mínimo 2 repositorios independientes:

- Aplicación principal
- Contenedor o root

### Aplicación principal

Crear una aplicación Single SPA con el comando

```bash
create-single-spa --skipInstall
```

Seguir indicaciones del CLI con las opciones recomendadas:

- Seleccionar **"single-spa application / parcel"** en ***"Select type to generate"***
- Seleccionar el framework a usar **(react, vue, angular, svelte, other)** en ***"Wich framework do you want to use?"***
- Seleccionar **"yarn"** como manejador de paquetes en ***"Which package manager do you want to use?"***
- Aceptar el uso de Typescript en la aplicación
- Definir **"cm-sigoo"** como ***"Organization name"***

Si seleccionaste **"react"** como framework para la aplicación, puedes ir a ***[/examples/react](https://github.com/PipeOspina/sigoo-mf-root-template/tree/master/examples/react)*** para más información.

### Contenedor o root

Una vez creada y desplegada la aplicación Single SPA, puedes clonar este repositorio como root del proyecto Microfrontend, y seguir la configuración recomendada para tu proyecto:

- Ir al archivo ***[/src/index.ejs](https://github.com/PipeOspina/sigoo-mf-root-template/blob/5511ddb68440c4328e94ee27c7579d1902a30547/src/index.ejs#L55C114-L55C114)***, eliminar la linea 52 e insertar el importmap de tu aplicación en la linea 54:

```html
  <script type="systemjs-importmap">
    {
      "imports": {
 (-) -> "@single-spa/welcome": "https://unpkg.com/single-spa-welcome/dist/single-spa-welcome.js",
        "@cm-sigoo/root-config": "https://<%= htmlWebpackPlugin.options.host %>/cm-sigoo-root-config.js",
        "@cm-sigoo/common-react": "https://sigoo-mf-common-react-dot-cm-single-spa-mf.ue.r.appspot.com/cm-sigoo-common-react.js",
 (+) -> "@cm-sigoo/[app-name]": "[app-url]/cm-sigoo-[app-name].js"
      }
    }
  </script>
```

- Actualizar el archivo ***[/src/microfrontend-layout.html](https://github.com/PipeOspina/sigoo-mf-root-template/blob/5511ddb68440c4328e94ee27c7579d1902a30547/src/microfrontend-layout.html#L22)*** en la linea 22 para cargar y renderizar tu aplicación:

```html
<main>
  <route default>
  -> <application name="@cm-sigoo/[app-name]"></application>
  </route>
</main>
```

- Instalar paquetes:

```bash
yarn install
```

- Levantar el proyecto para probar en local:

```bash
yarn start
```

Si todo funciona correctamente puedes desplegar tu aplicación y empezar a ver tu proyecto de la Suite Sigoo con aplicaciones en tiempo real.

Puedes cambiar también el nombre de la pestaña del proyecto en el navegador, actualizando el archivo ***[/src/index.ejs](https://github.com/PipeOspina/sigoo-mf-root-template/blob/5511ddb68440c4328e94ee27c7579d1902a30547/src/index.ejs#L55C114-L55C114)*** en la etiqueta title de la linea 7:

```html
-> <title>[app-label-name]</title>
```
