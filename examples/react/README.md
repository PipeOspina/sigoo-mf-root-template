# Sigoo Microfrontend React App Example

En este ejemplo encontraras algunos cambios en los archivos de configuración de la aplicación para un correcto funcionamiento con la Suite Sigoo.

## Para Empezar

### Prerrequisitos

Haber creado una aplicación Single SPA con el framework de **"react"** especificado en el archivo [README.md](https://github.com/PipeOspina/sigoo-mf-root-template/tree/master) del root...

> Para iniciar la aplicación se recomienda tener instalado el CLI de ***[create-single-spa](https://single-spa.js.org/docs/create-single-spa)*** para crear la aplicación inicial y configurar su funcionalidad.
>
> Se recomienda además usar Yarn en una versión >=3 para el manejo de paquetes y NodeJS 18 para inicializar este contenedor y sus aplicaciones.
>
> ## Instalación
>
> Crear una aplicación Single SPA con el comando
>
> ```bash
> create-single-spa --skipInstall
> ```
> 
> Seguir indicaciones del CLI con las opciones recomendadas:
> 
> - Seleccionar **"single-spa application / parcel"** en ***"Select type to generate"***
> - Seleccionar **"react"** en ***"Wich framework do you want to use?"***
> - Seleccionar **"yarn"** como manejador de paquetes en ***"Which package manager do you want to use?"***
> - Aceptar el uso de Typescript en la aplicación
> - Definir **"cm-sigoo"** como ***"Organization name"***

## Configuración

Antes de instalar las dependencias se recomienda copiar el archivo **[.yarnrc.yml](https://github.com/PipeOspina/sigoo-mf-root-template/blob/master/.yarnrc.yml)** en la raíz del proyecto con el fin de definir **node_modules** como carpeta contenedora de dependencias y evitar errores en los editores de código.

Además, hacer un upgrade de las dependencias de **react**, **react-dom**, **@types/react** , **@types/react-dom** y **@testing-library/react**:

```bash
yarn up react react-dom @types/react @types/react-dom @testing-library/react
```

Se recomienda también instalar la librería de [MaterialUI](https://mui.com/material-ui/getting-started/) como se especifica en su [documentación](https://mui.com/material-ui/getting-started/installation/):

```bash
yarn add @mui/material @emotion/react @emotion/styled @mui/icons-material
```

Para instalar el tema definido para **Sigoo Apps** en la aplicación puedes seguir las siguientes instrucciones:

- Declarar el módulo de **"@cm-sigoo/common-react"** en el archivo ***/src/declarations.d.ts*** de la aplicación:

```typescript
declare module "@cm-sigoo/common-react" {
  type FC<P = {}> = import("react").FC<P>;
  type PropsWithChildren<P = {}> = import("react").PropsWithChildren<P>;

  type Theme = import("@mui/material").Theme;

  // Components
  /// Atoms
  const Logo: FC<import("react").SVGProps<SVGSVGElement>>;
  /// Molecules
  /// Organisms
  const AppLayout: FC<
    PropsWithChildren<{
      label?: string;
      theme?: Theme;
      search?: boolean;
      searchValue?: string;
      onSearchChange?: (value: string) => void;
    }>
  >;

  // Utils
  const theme: Theme;

  export { AppLayout, Logo, theme };
}
```

Para tener una version actualizada del modulo, ingresa al archivo ***[export.declarations.d.ts](https://github.com/PipeOspina/sigoo-mf-common-react/blob/master/export.declarations.d.ts)*** del proyecto de **[sigoo-mf-common-react](https://github.com/PipeOspina/sigoo-mf-common-react)**

- Agregar el modulo de **"@cm-sigoo/common-react"** en los **externals** del archivo ***webpack.config.js*** y su **importmap** para el desarrollo local:

```javascript
const { merge } = require("webpack-merge");
const singleSpaDefaults = require("webpack-config-single-spa-react-ts");

module.exports = (webpackConfigEnv, argv) => {
  const defaultConfig = singleSpaDefaults({
    orgName: "cm-sigoo",
    projectName: "toolbar",
    webpackConfigEnv,
    argv,
(+) standaloneOptions: {
(+)   importMap: {
(+)     imports: {
(+)       react: "https://unpkg.com/react@^18.2.0/umd/react.development.js",
(+)       "react-dom":
(+)       "https://unpkg.com/react-dom@^18.2.0/umd/react-dom.development.js",
(+)       "@cm-sigoo/common-react":
(+)         "https://sigoo-mf-common-react-dot-cm-single-spa-mf.ue.r.appspot.com/cm-sigoo-common-react.js",
(+)     },
(+)   },
(+) },
  });

  return merge(defaultConfig, {
    // modify the webpack config however you'd like to by adding to this object
(+) externals: [/^@cm-sigoo\/.+/, "react", "react-dom"],
  });
};
```

- Importar el componente de ***AppLayout*** y el tema propuesto desde la librería de **common-react** de la Suite Sigoo, ademas del componente de ***ThemeProvider*** de MaterialUI, aplicar el tema y encapsular la aplicación dentro de estos componentes en el archivo ***/src/root.component.tsx***:

```typescript
import { AppLayout, theme } from "@cm-sigoo/common-react";
import App from "[path-to-app]";

export default function Root() {
  return (
    <ThemeProvider theme={theme}>
      <AppLayout label="[app-label-name]">
        <App />
      </AppLayout>
    </ThemeProvider>
  );
}
```

Puedes también sobrescribir componentes del tema de MaterialUI y aplicarlo a la aplicación:

```typescript
// theme.ts
import { theme } from "@cm-sigoo/common-react";

const muiTheme = createTheme(theme, {
  palette: {
    secondary: {
      main: "#FF0000",
    },
  },
});

export default muiTheme;

// root.component.tsx
import { AppLayout } from "@cm-sigoo/common-react";
import App from "[path-to-app]";
import theme from "./theme"

export default function Root() {
  return (
    <ThemeProvider theme={theme}>
      <AppLayout label="[app-label-name]" theme={theme}>
        <App />
      </AppLayout>
    </ThemeProvider>
  );
}
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

- Digitamos ***`http://localhost:[port]/cm-sigoo-[app-name].js`*** y damos click en el botón **"Apply override"**

- Recargamos la página y comprobamos

Si encontramos algún error podemos dirigirnos a nuestra aplicación minificada que lanzamos en local `localhost:[port]` y seguir las instrucciones que aparezcan en pantalla.

Si todo funciona correctamente, podrás ver los cambios en tiempo real de nuestra aplicación local en un entorno de despliegue.
