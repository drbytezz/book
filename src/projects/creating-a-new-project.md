## Creando un nuevo proyecto

Para iniciar un nuevo proyecto con Foundry, use [`forge init`](../reference/forge/forge-init.md):

```sh
{{#include ../output/hello_foundry/forge-init:command}}
```
Este comando crea una nueva carpeta llamada `hello_foundry`, la cual pertenece a las plantillas por defecto. Por otra parte, esto inicia un nuevo repositorio en `git`.

Si el lector deseea crear un nuevo proyecto usando una plantilla diferente, debe pasar el siguiente identificador `--template`, como puede ser el siguiente ejemplo:

```sh
$ forge init --template https://github.com/foundry-rs/forge-template hello_template
```

Por ahora, veamos cómo se ve la plantilla predeterminada:

```sh
$ cd hello_foundry
{{#include ../output/hello_foundry/tree:all}}
```

Esta plantilla viene con una dependencia instalada: Biblioteca estándar de Forge. Esta es la biblioteca de pruebas preferida utilizada para proyectos de Foundry. Además, la plantilla también viene con un contrato inicial vacío y una prueba sencilla.

Compilemos el proyecto:

```sh
{{#include ../output/hello_foundry/forge-build:all}}
```

Y para correr los tests:

```sh
{{#include ../output/hello_foundry/forge-test:all}}
```
Seguro el lector notará que han aparecido dos nuevas carpetas: `out` y` cache`.

La carpeta `out` contiene el artefacto de su contrato, como el ABI, mientras que` forge` usa el `cache` para recompilar solo lo que es necesario.
