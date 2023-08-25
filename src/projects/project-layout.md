## Diseñando un proyecto

Forge es una herramienta flexible al momento de diseñar tu proyecto. Por defecto, la estructura es:

```ignore
{{#include ../output/hello_foundry/tree-with-files:output}}
```

- El lector puede configurar el comportamiento de Foundry desde el archivo `foundry.toml`.
- Los "remappings" se especifican en `remappings.txt`.
- La carpeta por defecto donde se encuentran los contratos es `src/`.
- La carpeta por defecto donde se encuentran  `test/`, dondo para todo contrato que contenga alguna funcion con un prefijo `test`, se lo considera un test.
- Las dependencias se guardan como  submodulos de git en  `lib/`.


El lector puede configurar dónde busca Forge tanto las dependencias como los contratos utilizando los indicadores `--lib-tools` y` --contracts` respectivamente. Alternativamente, es posible configurarlo en `foundry.toml`.

Combinado con "remappings", esto le da al lector la flexibilidad necesaria para respaldar la estructura del proyecto de otras herramientas como Hardhat y Truffle.

Para obtener compatibilidad automática con Hardhat, también puede pasar el indicador `--hh`, que establece los siguientes indicadores:` --lib-External node_modules --contracts development`.
