## Dependencias

Forge gestiona las dependencias utilizando [submódulos de git](https://git-scm.com/book/en/v2/Git-Tools-Submodules) de forma predeterminada, lo que significa que funciona con cualquier repositorio de GitHub que contenga contratos inteligentes.

### Agregando una dependencias

Para agregar una dependencia, ejecute el comando [`forge install`](../reference/forge/forge-install.md):

```sh
{{#include ../output/deps/forge-install:all}}
```

Esto extrae la dependencia `solmate`, modifica el archivo` .gitmodules` en git y una vez culminada la instalación, realiza una confirmación con el mensaje(en inglés) "Solmate instalado" en la consola.

Si ahora revisamos la carpeta `lib`:

```sh
{{#include ../output/deps/tree:all}}
```

Podemos observar que Forge instaló `solmate` de forma correcta!

Por defecto, `forge install` instala la última versión de la rama maestra. Si el lector desea instalar una etiqueta o un "commit" específico, puede hacerlo así:

```sh
$ forge install transmissions11/solmate@v7
```

### Reasignando dependencias

Forge es capaz de reasignar dependencias para hacer que utilizarlas sea mas sencillo y/o conveniente para el usuario. De forma automática, Forga va a deducir, o por lo menos, a intentar deducir algunas de estas reasignaciones sin que tengas que pedirlo:

```sh
{{#include ../output/deps/forge-remappings:all}}
```

Estas reasignaciones significan:

- Para importar `forge-std` debemos escribir: `import "forge-std/Contract.sol";`
- Para importar `ds-test` debemos escribir: `import "ds-test/Contract.sol";`
- Para importar `solmate` debemos escribir: `import "solmate/Contract.sol";`
- Para importar `weird-erc20` debemos escribir: `import "weird-erc20/Contract.sol";`

El lector puede modificar estos "remappings" creando un archivo `remappings.txt` en la base del proyecto.

Let's create a remapping called `solmate-utils` that points to the `utils` folder in the solmate repository!

```sh
solmate-utils/=lib/solmate/src/utils/
```

You can also set remappings in `foundry.toml`.

```toml
remappings = [
    "@solmate-utils/=lib/solmate/src/utils/",
]
```

Ahora podemos importar cualquiera de los contratos en `src/utils` del repositorio de solmate así:

```solidity
import "solmate-utils/LibString.sol";
```

### Actualización de dependencias

También es posible actualizar una dependencia específica al último "commit" de la versión que haya especificado usando [`forge update <dep>`](../reference/forge/forge-update.md). Por ejemplo, si quisiéramos obtener el último "commit" de nuestra versión maestra previamente instalada de `solmate`, ejecutaríamos lo siguiente:

```sh
$ forge update lib/solmate
```

De forma alternativa, el lector  puede hacer esto para todas las dependencias a la vez simplemente ejecutando `forge update`.

### Eliminando dependencias

Es posible eliminar dependencias usando [`forge remove <deps>...`](../reference/forge/forge-remove.md), donde `<deps>` es la ruta completa a la dependencia o solo el nombre. . Por ejemplo, para eliminar `solmate`, ambos comandos son equivalentes:

```ignore
$ forge remove solmate
# ... is equivalent to ...
$ forge remove lib/solmate
```

### Compatibilidad con Hardhat

Forge también admite proyectos estilo Hardhat donde las dependencias son paquetes npm (almacenados en `node_modules`) y los contratos se almacenan en` development` en lugar de `src`.

Para habilitar el modo de compatibilidad con Hardhat, pase el indicador `--hh`.
