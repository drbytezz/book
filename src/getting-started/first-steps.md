## Primeros pasos en Foundry

Esta sección provee al lector una visión general sobre las herramientas de línea de comandos inlcuidas en `forge`. Se va a demostrar como crear un nuevo proyecto, como compilarlo y como hacer test para el mismo.

Para crear un nuevo proyecto con Foundry, usa [`forge init`](../reference/forge/forge-init.md):

```sh
{{#include ../output/hello_foundry/forge-init:command}}
```

Veamos qué generó `forge` para nosotros:

```sh
$ cd hello_foundry
{{#include ../output/hello_foundry/tree:all}}
```

Podemos compilar este proyecto con[`forge build`](../reference/forge/forge-build.md):

```sh
{{#include ../output/hello_foundry/forge-build:all}}
```

Y correr los test con [`forge test`](../reference/forge/forge-test.md):

```sh
{{#include ../output/hello_foundry/forge-test:all}}
```
<br>

> 💡 **Consejo**
> 
>Siempre podés obtener ayuda para cualquier subcomando (o sus subcomandos) al agregar `--help` al final.

En caso que tengas un tipo de aprendizaje visual, podés mirar  [estos](../tutorials/learn-foundry.md) tutoriales para principiantes.
