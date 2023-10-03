# Sigoo Microfrontend React App Example

En este ejemplo encontraras algunos cambios en los archivos de configuración de la aplicación para un correcto funcionamiento con la Suite Sigoo.

## Para Empezar

### Prerrequisitos

Haber creado una aplicación Single SPA con el framework de **"react"** especificado en el archivo [README.md](https://github.com/PipeOspina/sigoo-mf-root-template/tree/master) del root.

## Configuración

Antes de instalar las dependencias se recomienda copiar el archivo **[.yarnrc.yml]()** en la raíz del proyecto con el fin de definir **node_modules** como carpeta contenedora de dependencias y evitar errores en los editores de código.

Además, hacer un upgrade de las dependencias de **@types/react** y **@types/react-dom**:

```bash
yarn up @types/react @types/react-dom
```

Se recomienda también instalar la librería de [MaterialUI](https://mui.com/material-ui/getting-started/) como se especifica en su [documentación](https://mui.com/material-ui/getting-started/installation/):

```bash
yarn add @mui/material @emotion/react @emotion/styled @mui/icons-material
```

Para instalar el tema definido para **Sigoo Apps** en la aplicación puedes seguir las siguientes instrucciones:

- Declarar el módulo de **"@cm-sigoo/utilities"** en el archivo ***/src/declarations.d.ts*** de la aplicación:

```typescript
declare module "@cm-sigoo/utilities" {
  const muiTheme: import("@mui/material").Theme;
}
```

- Agregar el modulo de **"@cm-sigoo/utilities"** en los **externals** del archivo ***webpack.config.js*** y su **importmap** para el desarrollo local:

```javascript
const { merge } = require("webpack-merge");
const singleSpaDefaults = require("webpack-config-single-spa-react-ts");

const port = process.env.port || 8080;

module.exports = (webpackConfigEnv, argv) => {
  const defaultConfig = singleSpaDefaults({
    orgName: "cm-sigoo",
    projectName: "toolbar",
    webpackConfigEnv,
    argv,
(+) standaloneOptions: {
(+)   importMap: {
(+)     imports: {
(+)       "@cm-sigoo/utilities":
(+)         "https://sigoo-mf-utilities-dot-cm-single-spa-mf.ue.r.appspot.com/cm-sigoo-utilities.js",
(+)     },
(+)   },
(+) },
  });

  return merge(defaultConfig, {
    // modify the webpack config however you'd like to by adding to this object
(+) externals: [/^@cm-sigoo\/.+/],
  });
};
```

- Importar ***muiTheme*** desde la librería de utilidades de la Suite Sigoo y encapsular la aplicación en el componente **ThemeProvider** de MaterialUI en el archivo ***/src/root.component.tsx***:

```typescript
import { muiTheme } from "@cm-sigoo/utilities";
import { ThemeProvider } from "@mui/material";
import App from "[path-to-app]";

export default function Root() {
  return (
    <ThemeProvider theme={muiTheme}>
      <App />
    </ThemeProvider>
  );
}
```

Si deseas puedes importar también la aplicación de **Toolbar** propuesta para la Suite Sigoo así:

- Declarar el módulo de **"@cm-sigoo/toolbar"** en el archivo ***/src/declarations.d.ts*** de la aplicación:

```typescript
declare module "@cm-sigoo/toolbar" {
  const toolbarConfig: import("single-spa-react").ReactAppOrParcel<{
    appLabel?: string;
    children?: React.ReactNode;
  }>;
  export default toolbarConfig;
}
```

- Actualizar el archivo ***/src/root.component.tsx***:

```typescript
(+) import toolbarConfig from "@cm-sigoo/toolbar";
    import { muiTheme } from "@cm-sigoo/utilities";
    import { ThemeProvider } from "@mui/material";
(+) import Parcel from "single-spa-react/parcel";
    import App from "[path-to-app]";

    export default function Root() {
      return (
        <ThemeProvider theme={muiTheme}>
(+)       <Parcel config={toolbarConfig} appLabel="[app-label-name]">
            <App {...props} />
(+)       </Parcel>
        </ThemeProvider>
      );
    }
```

- Agregar el **importmap** de **"@cm-sigoo/toolbar"** para el desarrollo local:

```javascript
const { merge } = require("webpack-merge");
const singleSpaDefaults = require("webpack-config-single-spa-react-ts");

const port = process.env.port || 8080;

module.exports = (webpackConfigEnv, argv) => {
  const defaultConfig = singleSpaDefaults({
    orgName: "cm-sigoo",
    projectName: "toolbar",
    webpackConfigEnv,
    argv,
    standaloneOptions: {
      importMap: {
        imports: {
          "@cm-sigoo/utilities":
            "https://sigoo-mf-utilities-dot-cm-single-spa-mf.ue.r.appspot.com/cm-sigoo-utilities.js",
(+)       "@cm-sigoo/toolbar":
(+)         "https://sigoo-mf-toolbar-dot-cm-single-spa-mf.ue.r.appspot.com/cm-sigoo-toolbar.js",
        },
      },
    },
  });

  return merge(defaultConfig, {
    // modify the webpack config however you'd like to by adding to this object
    externals: [/^@cm-sigoo\/.+/],
  });
};
```

- Levantar el proyecto para probar en local:

```bash
yarn start:standalone
```

Si todo funciona correctamente puedes desplegar tu aplicación y empezar a ver tus cambios en tiempo real.

## Arriba y local

Para probar nuestra aplicación podemos levantar el proyecto localmente con:

```bash
yarn start:standalone
```

Pero podemos probar nuestros cambios locales de la aplicación (sin desplegar) directamente en el servidor donde se haya desplegado el root gracias a que Single SPA implementa los [import-maps-overrides](https://github.com/single-spa/import-map-overrides) así:

- Lanzamos nuestra version minificada de la aplicación en local:

```bash
yarn start
```

- Abrimos la consola en la web de nuestro **root** y agregamos un nuevo item en el localStorage para un entorno de desarrollo:

```javascript
localStorage.setItem('devtools', true)
```

- Recargamos la página.

- Aparece un icono de llaves **"{...}"** en la parte inferior derecha de la página donde podremos dar clic.

- Buscamos en la columna de ***Module Name*** el nombre de nuestra aplicación **"@cm-sigoo/[app-name]"** y damos click sobre él.

- Digitamos ***`localhost:[port]/cm-sigoo-[app-name].js`*** y damos click en el botón **"Apply override"**

- Recargamos la página y comprobamos

Si encontramos algún error podemos dirigirnos a nuestra aplicación minificada que lanzamos en local `localhost:[port]` y seguir las instrucciones que aparezcan en pantalla.

Si todo funciona correctamente, podrás ver los cambios en tiempo real de nuestra aplicación local en un entorno de despliegue.