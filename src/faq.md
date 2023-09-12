## FAQ

Esta es una colección de preguntas y respuestas frecuentes. Si no encuentra su pregunta aquí, ingrese al [Telegram support channel][tg-support]
y ¡déjanos ayudarte!

### `libusb` Error Al Ejecutar  `forge`/`cast`

Si está utilizando los binarios tal como se publicaron, es posible que vea el siguiente error en MacOS:

```sh
dyld: Library not loaded: /usr/local/opt/libusb/lib/libusb-1.0.0.dylib
```

Para solucionar este problema, debe instalar la libreria `libusb`:

```sh
brew install libusb
```

### Error `GLIBC` Anticuado :

Si se encuentra con un error similar al siguiente después de usar`foundryup`:

```sh
forge: /lib/x86_64-linux-gnu/libc.so.6: version 'GLIBC_2.29' not found (required by forge)
```

Existen 2 soluciones:

1. [Construirlo desde la fuente](./getting-started/installation.md#building-from-source)
2. [Usar Docker](./getting-started/installation.md#using-foundry-with-docker)

### ¡Ayuda! No puedo ver mis registros

Forge no muestra registros por defecto. Si desea ver registros de Hardhat del tipo `console.log` o eventos del estilo DSTest `log_*`,
necesitas ejecutar [`forge test`][forge-test] con verbosidad 2 (`-vv`).

Si desea ver otros eventos que emiten sus contratos, debe ejecutarlos con los rastreos habilitados.
Para hacer eso, establezca la verbosidad en 3 (`-vvv`) para ver rastros de pruebas fallidas, o 4 (`-vvvv`)para ver rastros de todas las pruebas.

### Mis pruebas fallan y no sé por qué.

Para obtener una mejor idea de por qué fallan las pruebas, intente utilizar seguimientos. Para habilitar los seguimientos, debe aumentar la verbosidad
en [forge test][forge-test] a, por lo menos 3(`-vvv`), pero se puede ir tan alta como 5 (`-vvvvv`) para ver aún más rastros.

Puedes conocer más sobre las trazas en nuestro capitulo [Entendiendo las trazas][traces].

### ¿Cómo uso `console.log`?

Para usar `console.log` incluido en Hardhat, debe agregarlo a su proyecto copiando el archivo desde [aquí][console-log].

De forma alternativa, el lector puede usar [Forge Std][forge-std] el cual viene con la dependencia `console.log` incluida. Para usar  `console.log` desde Forge Std,tienes que importarlo:

```solidity
import "forge-std/console.sol";
```

### ¿Cómo ejecuto _test_ específicos?

Si desea ejecutar solo algunas pruebas, puede usar `--match-test` para filtrar funciones de prueba,
`--match-contract` para filtrar contratos de prueba, y `--match-path` para filtrar archivos de prueba en [`forge test`][forge-test].

### ¿Cómo uso un compilador específico de Solidity?

Forge intentará detectar automáticamente qué compilador Solidity funciona para su proyecto.

Para utilizar un compilador de Solidity específico, puede configurar [`solc`][config-solc] en su archivo [config][config],
o pasar a forge el comando `--use solc:<version>` una version que tenga soport (e.j. [`forge build`][forge-build]
o [`forge test`][forge-test]).
También se aceptan rutas a un binario solc. Para utilizar un binario solc local específico, puede configurar`solc = "<path to solc>"` ien su archivo de configuración, o pasar el comando `--use "<path to solc>"`.
La versión/ruta de solc también se puede configurar mediante la variable env `FOUNDRY_SOLC=<version/path>`,pero el argumento del CLI `--use` tiene prioridad.

Por ejemplo, si tiene un proyecto que admite todas las versiones 0.7.x de Solidity, pero desea compilar con solc 0.7.0, puede usar`forge build --use solc:0.7.0`.

### ¿Cómo hago un _fork_ desde una red?

Para hacer un _fork_ desde una red , pase`--fork-url <URL>` al [`forge test`][forge-test].
También se puede realizar _forks_ desde un bloque específico usando`--fork-block-number <BLOCK>`, lo que agrega determinismo a su prueba y permite a Forge almacenar en caché
los datos de la cadena para ese bloque.

Por ejemplo, para hacer _forks_ desde la red principal de Ethereum en el bloque 10.000.000, podrías usar:`forge test --fork-url $MAINNET_RPC_URL --fork-block-number 10000000`.

### ¿Cómo agrego mis propias afirmaciones?

Puede agregar sus propias afirmaciones creando su propio contrato de prueba base y haciéndolo heredar del marco de prueba de su elección.

Por ejemplo, si usa DSTest, podría crear un contrato de prueba base como este:

```solidity
contract TestBase is DSTest {
    function myCustomAssertion(uint a, uint b) {
      if (a != b) {
          emit log_string("a and b did not match");
          fail();
      }
    }
}
```

Deberías heredar del contrato `TestBase` en todos tus contratos de _test_ para poder utilizar esta afirmacion customizada.

```solidity
contract MyContractTest is TestBase {
    function testSomething() {
        // ...
    }
}
```

Del mismo modo, si utiliza [Forge Std][forge-std], puede crear un contrato de prueba base que herede de `Test`.

Para ver un buen ejemplo de un contrato de prueba base que tiene métodos auxiliares y aserciones personalizadas, consulte [`DSTestPlus` de Solmate ][dstestplus].

### ¿Cómo uso Forge sin conexión?

En ocasiones, Forge buscará versiones más nuevas de Solidity que se ajusten a su proyecto. Para utilizar Forge sin conexión, utilice la marca `--offline`.

### Recibo errores de Solc

[solc-bin](https://binaries.soliditylang.org/) no ofrece compilaciones estáticas para Apple Silicon. Foundry se basa en [svm](https://github.com/roynalnaruto/svm-rs)para instalar compilaciones nativas para dichos dispositivos.

Todas las versiones de solc se instalan bajo `~/.svm/`. Si encuentra errores relacionados con solc, como `SolcError: ...`, intente eliminar  `~/.svm/` e inténtelo de nuevo, esto activará una nueva instalación y normalmente resuelve el problema.

Si utiliza Apple Silicon, asegúrese de que la [validacion del teorema `z3`](https://github.com/Z3Prover/z3) esté instalado: `brew install z3`

> **Nota**: las versiones nativas de Apple Silicon solo están disponibles de la version `0.8.5` en adelante. Si necesita versiones anteriores, debe habilitar Apple Silicon Rosetta para ejecutarlas.

### Forge falla en monorepos de JavaScript (`pnpm`)

Los administradores como `pnpm` usan enlaces simbólicos para administrar las carpetas `node_modules`.

Un patron común puede verse así:

```text
├── contracts
│    ├── contracts
│    ├── foundry.toml
│    ├── lib
│    ├── node_modules
│    ├── package.json
├── node_modules
│    ├── ...
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml
```

Dónde el espacio de trabajo de Foundry está en `./contracts`, pero los paquetes instalados se encuentran en `./contracts/node_modules` los cuales se vinculan de forma simbolica a  `./node_modules`.

AL momento de ejecutar `forge build` en `./contracts/node_modules`, esto puede provocar un error como:

```console
error[6275]: ParserError: Source "node_modules/@openzeppelin/contracts/utils/cryptography/draft-EIP712.sol" not found: File outside of allowed directories. The following are allowed: "<repo>/contracts", "<repo>/contracts/contracts", "<repo>/contracts/lib".
 --> node_modules/@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol:8:1:
  |
8 | import "../../../utils/cryptography/draft-EIP712.sol";
```

Este error aparece cuando `solc` es capaz de resolver el vinculo entre los archivos, pero estos se encuentran por fuera del entorno de trabajo de Foundry (`./contracts`).

Si usted agrega `node_modules` a `allow_paths` en `foundry.toml`, usted garantiza el acceso a solc a dicho directorio, y este va a ser capaz de utilizar el contenido del mismo:

```toml
# This translates to `solc --allow-paths ../node_modules`
allow_paths = ["../node_modules"]
```

Tenga en cuenta que la ruta es relativa al entorno de trabajo de Foundry. Vease también las[rutas permitidas para solc](https://docs.soliditylang.org/en/latest/path-resolution.html#allowed-paths)

### Recibo `Permission denied (os error 13)`

Si ves un error como

```console
Failed to create artifact parent folder "/.../MyProject/out/IsolationModeMagic.sol": Permission denied (os error 13)
```

Entonces es probable que haya un problema con los permisos de la carpeta. Asegúrese de que el `usuario` tenga acceso de escritura en la carpeta raíz del proyecto.

Se ha [reportado](https://github.com/foundry-rs/foundry/issues/3268) que en linux, that on linux, canonicalizar rutas puede resultar en rutas extrañas (`/_1/...`). Esto se puede resolver eliminando toda la carpeta del proyecto e inicializándola nuevamente.

### Conexión rechazada al ejecutar `forge build` .

Si no puede acceder a las URL de github llamadas por `forge build`, verá un error como

```console
Error:
error sending request for url (https://raw.githubusercontent.com/roynalnaruto/solc-builds/ff4ea8a7bbde4488428de69f2c40a7fc56184f5e/macosx/aarch64/list.json): error trying to connect: tcp connect error: Connection refused (os error 61)
```

La conexión falló porque el acceso a la URL desde su ubicación puede estar restringido. Para resolver esto, debes configurar el proxy.

Usted puede ejecutar `export http_proxy=http://127.0.0.1:7890 https_proxy=http://127.0.0.1:7890` primero en su terminal, y luego ejecutar  `forge build` de forma exitosa.

[tg-support]: https://t.me/foundry_support
[forge-test]: ./reference/forge/forge-test.md
[traces]: ./forge/traces.md
[config-solc]: ./reference/config/solidity-compiler.md#solc_version
[config]: ./config/
[forge-build]: ./reference/forge/forge-build.md
[console-log]: ./reference/forge-std/console-log.md
[forge-std]: https://github.com/foundry-rs/forge-std
[dstestplus]: https://github.com/transmissions11/solmate/blob/19a4f345970ed39ee6369f343d145e0d4071c18a/src/test/utils/DSTestPlus.sol#L10
