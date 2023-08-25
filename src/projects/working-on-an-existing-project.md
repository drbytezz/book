## Trabajando en un proyecto que ya existe

Si el lector descarga un proyecto existente que utiliza Foundry, es realmente fácil comenzar.

Primero, obtenga el proyecto de alguna parte. En este ejemplo, clonaremos el repositorio `femplate` de GitHub:

```sh
$ git clone https://github.com/abigger87/femplate
$ cd femplate
$ forge install
```

Ejecutamos [`forge install`](../reference/forge/forge-install.md) para instalar las dependencias del submódulo que están en el proyecto.

Para compilar, utilice [`forge build`](../reference/forge/forge-build.md):

```sh
{{#include ../output/femplate/forge-build:all}}
```

Y para testear, utilice [`forge test`](../reference/forge/forge-test.md):

```sh
{{#include ../output/femplate/forge-test:all}}
```
