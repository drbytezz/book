# Buenas Prácticas 

El siguiente documento sugiere algunos patrones que son considerados por la comunidad como buenas prácticas al momnto de desarrollar un proyecto con Foundry.
Por lo general, se recomienda manejarse con [`forge fmt`](../reference/config/formatter.md), siendo esta la herramienta para formatear el código que viene incluida. COmo toda herramienta, esta presenta ciertas limitaciones, las cuales se comentan aquí debajo.

- [Guía General para montar Contratos](#guía-general-para-montar-contratos)
- [Tests](#tests)
  - [Guía General para montar Tests](#guía-general-para-montar-tests)
  - [Fork Tests](#fork-tests)
  - [Arneses de prueba](#arneses-de-prueba)
    - [Funciones Internas](#funciones-internas)
    - [Funciones Privadas](#funciones-privadas)
    - [Alternativas a Fucniones](#alternativas-a-funciones)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Análisis de contaminación](#análisis-de-contaminación)
- [Scripts](#scripts)
- [Comentarios](#comentarios)
- [Recursos](#recursos)

## Guía General para montar Contratos

1. Es muy recomendable que al momento de importar un archivo, no se importe el archivo completo per se, sino que se importen los componentes que son necesarios. A esto lo denominamos la importación por nombre. Esto restringe lo que se importa solo a los elementos nombrados, no a todo el contenido del archivo. La importación de archivos completos puede hacer que solc se queje de definiciones duplicadas y errores de deslizamiento, especialmente a medida que los repositorios crecen y tienen más dependencias con nombres superpuestos.

   - Correcto: `import {MyContract} from "src/MyContract.sol"` se restringe a importar `MyContract`.
   - Incorrecto: `import "src/MyContract.sol"` importa la totalidad`MyContract.sol`. (Importar `forge-std/Test` o `Script` se pueden excluir de esta lista, de esta foma obtenes la libreria de `console.log` entre otras inlcuidas).

1. Mantenga un orden al importar. Importe primero por `forge-std/`, luego por dependencias, `test/`, `script/` y finalmente por `src/`. Dentro de cada uno, ordene alfabéticamente por ruta (no por los elementos con nombres explícitos que se importan). _(Nota: esto se puede eliminar una vez que [foundry-rs/foundry#3396](https://github.com/foundry-rs/foundry/issues/3396) se fusione)._

1. De forma análoga, ordene los imports por nombre. _(Nota: esto se puede eliminar una vez que se resuelva [foundry-rs/foundry#3396](https://github.com/foundry-rs/foundry/issues/3396))._

   - Correcto: `import {bar, foo} from "src/MyContract.sol"`
   - Incorrecto: `import {foo, bar} from "src/MyContract.sol"`


1. Tenga en cuenta las diferencias entre importar un archivo con rutas absolutas y con rutas relativas (donde las rutas absolutas son relativas a la raíz de la carpeta, por ejemplo, `"src/interfaces/IERC20.sol"`), y elija el mejor enfoque para su proyecto::

   - Las rutas absolutas facilitan ver de dónde provienen los archivos y reducen la rotación al mover archivos.
   - Las rutas relativas hacen que sea más probable que su editor pueda proporcionar funciones como linting y autocompletar, ya que es posible que los editores/extensiones no comprendan sus reasignaciones.

1. Si el lector decide copiar una libreria de una dependencia (en lugar de importarla), use la opción `ignore = []` en el archivo de configuración para evitar formatear ese archivo. Esto hace que compararlo con el original sea más sencillo para los revisores y auditores.

1. De forma similar, sientase libre de utilizar los comentarios `// forgefmt: disable-*` para que al momento de formatear el archivo, dichas lineas/secciones sean ignoradas; ya sea porque el lector prefiere como se ven o considera que un formateo manual es más apropiado. Los valores para los que Foundry ofrece soporte (`*`) son los siguientes:

   - `disable-line`
   - `disable-next-line`
   - `disable-next-item`
   - `disable-start`
   - `disable-end`

Otro recurso que, si el lector está interesado en buenas prácticas, puede revisar las redes de [samsczun](https://twitter.com/samczsun) o este excelente video suyo donde trata el tema. Se titula [How Do You Even Write Secure Code Anyways](https://www.youtube.com/watch?v=Wm3t8Fuiy1E) :

- EL nombre de sus variables debe ser descriptivo.
- Limitar el número de variables activas.
- Evitar que haya lógica redundante.
- Salida anticipada tanto como sea posible para reducir la carga mental al ver el código.
- Las funciones que estén relacionadas deben posicionarse cerca.
- Evitar el código que no se utiliza.

## Tests

### Guía General para montar Tests

1. Para realizarle test a `MyContract.sol`,el nombre del archivo debería ser `MyContract.t.sol`. Para realizarle test a `MyScript.s.sol`, el nombre del archivo debería ser `MyScript.t.sol`.

   - En caso de que su archivo sea muy grande, y el lector desee fragmentarlo por comodidad, esto se puede lograr con una agrupacion lógica, la cual nombre el contrato, seguido de la sección que se deaea observar terminando con el prefijo de `.t.sol`. Ejemplo de esto puede ser lo siguiente: `MyContract.owner.t.sol`, `MyContract.deposits.t.sol`, etc.

1. No se recomienda el uso de condicionales dentro de la funcion `setUp`, en vez de ponerlas ahí, se recomienda incluirlas dentro de una función que estén dedicada a aquella condicio, un ejemplo  de esto es `test_SetUpState()`. Si lector desea explorar el funcionamiento de la función `setUp`, dirijase al siguiente "issue" de github : [foundry-rs/foundry#1291](https://github.com/foundry-rs/foundry/issues/1291)

1. Por el momento, son 2 las formas principales de realizar test por unidades, se describen a continuacion:

   1. Se trata los contratos como bloques descriptivos. Esto implica que el lector, al momento de realizar sus tests, generara nuevos contratos los cuales conendran los tests especificos de cada bloque : 
      - `contract Add` contiene los test para el metodo `add` del contrato `MyContract`.
      - `contract Supply` contiene los test para el metodo `supply`.
      - `contract Constructor` contiene los test para el metodo "constructor".
      - Este acercamiento puede resultar beneficioso al momento de realizar tests mediante contratos pequeños y especificos debido a que estos compilan a mayor velocidad que archivos más pesados. Es realmente util al momento de manejar un "suite" de tests muy grande, los tiempos de compilacion deberian verse reducidos significativamente.
   2. Para este acercamiento, el lector genera un solo contrato el cual engloba todos los test del contrato "target", esto implica realizar pruebas para tantas funciones y escenarios se presenten en dicho contrato:
      - `contract MyContractTest` contiene todos los test para`MyContract`.
      - `function test_add_AddsTwoNumbers()` la encontramos en  `MyContractTest`, lo que resuelve los tests de la funcion `add`.
      - `function test_supply_UsersCanSupplyTokens()` también la encontramos en `MyContractTest`,lo que resuelve los tests de la funcion `supply`.
      - Este acercamiento no solo le permite al lector agrupar todos los escenarios, lo que le permite comprender más a fondo lo sucedido en dicho contrato; sino que tambien permite identificar fallas no tan evidentes del diseño del contrato/protocolo.

1. Lineamientos generales para realizar todos los tests:

   - Los test/contratos deberían escribirse en el mismo orden que aparecen en el contratoq ue esta siendo testeado.
   - Todo `unit tests` que realize pruebas sobre una funcion particular deberían estar agrupadas de forma serial en un mismo archivo.
   - Los contratos encargados de realizar los test pueden tener heredar funciones y/o estados de cualquier contrato `helper`. Un ejemplo de esto puede ser el siguiente: el `contract MyContractTest` es donde, segun hemos descrito, deben estar los test del contrato `MyContract`, ahora bien, ese contrato puede tener la dependencia  `Test` incluida en la suite de forge-std. Como tambien puede ser el caso de que tu construyas un suite personalizado llamado `TestUtilities` y desees utilizarlo en tus test futuros.

1. Las pruebas de integración deben ubicarse en el mismo directorio `test`, con una convención de nombres clara. Estas pruebas pueden estar en archivos dedicados, o pueden convivir junto a las pruebas unitarias relacionadas en archivos de prueba existentes.

1. Se recomienda mantener la consistencia en la nomenclatura de las pruebas, ya que es útil para filtrar pruebas (por ejemplo, para informes de gas, es posible que desees filtrar las pruebas que revierten). Cuando combines convenciones de nombres, mantenlas en orden alfabético. A continuación, se muestra un ejemplo de nombres válidos. Podes encontrar una lista completa de ejemplos válidos e inválidos en el siguiente [documento](https://github.com/ScopeLift/scopelint/blob/1857e3940bfe92ac5a136827374f4b27ff083971/src/check/validators/test_names.rs#L106-L143).

   - `test_Description` para test normales .
   - `testFuzz_Description` para test de fuzz.
   - `test_Revert[If|When]_Condition` para test que esperas reviertan.
   - `testFork_Description`  para test que interactuen con un "fork" de alguna red.
   - `testForkFuzz_Revert[If|When]_Condition` para test que combinan todos los anteriores, un tes que hace "fuzz" en un "fork" y, como si fuera poco, se espera que este test revierta.

1. Es muy recomendable nombrar las variables que sean constantes y/o inmutables usando `TODO_MAYUS_USANDO_GUION_BAJO`, esto hace que distingurlas de funciones sea mucho mas sencillo.

1. Cuando queremos probar asersiones, como puede ser el caso de  `assertEq`, el lector debe considerar el ultimo parametro para identificar de forma eficaz y sencilla los posibles fallos. No tiene necesidad de ser extremadamente detallado, se pueden mantener breves y consisos o inclusive pueden ser un numero&mdash; basicamente sirven para reemplazar numeros planos al momento de reertir la accion, por ejemplo `assertEq(x, y, "1")` o `assertEq(x, y, "sum1")`. _(Note: [foundry-rs/foundry#2328](https://github.com/foundry-rs/foundry/issues/2328) tracks integrating this natively)._

1. Cuando el lector desee realizar test para eventos, es preferible setear todos los parametros de `expectEmit` como `true`,por ejemplo. `vm.expectEmit(true, true, true, true)` o `vm.expectEmit()`. Beneficios:

   - Esto asegura que pruebes todo en tu evento.
   - Si agregas un parametro (es decir, un nuevo parámetro indexado), ahora se prueba automáticamente.
   - Incluso si solo tienes 1 parametro, los argumentos adicionales true no afectan la prueba.

1. ¡No te olvides de escribir pruebas de invariantes! Para la aserción, usá una descripción en inglés detallada del invariante: `assertEq(x + y, z, "Se rompió el invariante: la suma de x e y siempre debe ser igual a z")`. Para obtener más información sobre esto, dale una mirada al tutorial de [Pruebas de Invariantes](../forge/invariant-testing).
.
### Pruebas de Fork 

1. No te sientas obligado a tratar las pruebas de forks de manera especial, y úsalas sin restricciones:

   - Los mocks son _necesarios_ en el desarrollo web2 de código cerrado; debes simular respuestas de API porque el código de esa API no es de código abierto, por lo que no puedes simplemente ejecutarlo localmente. Pero en el caso de las blockchains eso no es cierto: cualquier código con el que estás interactuando y que ya está desplegado puede ejecutarse localmente e incluso modificarse de forma gratuita. Entonces, ¿por qué introducir el riesgo de un mock incorrecto si no es necesario?
   - Una razón común para evitar las pruebas de forks y preferir los mocks es que las pruebas de forks son lentas. Pero esto no siempre es cierto. Al fijar un número de bloque, Forge almacena en caché las respuestas de RPC, por lo que solo la primera ejecución es más lenta y las ejecuciones posteriores son considerablemente más rápidas. Revisate [estos benchmark](https://github.com/mds1/convex-shutdown-simulation/), donde a Forge le llevó 7 minutos para la primera ejecución con un RPC remoto, pero solo medio segundo una vez que los datos se almacenaron en caché. [Alchemy](https://alchemy.com), [Infura](https://infura.io) y [Tenderly](https://tenderly.co) ofrecen datos de archivo de forma gratuita, por lo que fijar a un bloque no debería ser problemático.
   - Deberas notar que [herramientas compatibles con foundry](https://github.com/foundry-rs/foundry-toolchain) como GitHub Action guardan los caches de las respuestas de RPC en CI de forma predeterminada, y también actualizará la caché cuando actualices tus pruebas de forks.

1. Ten cuidado con las pruebas de fuzzing en un fork para evitar consumir tus solicitudes RPC con pruebas de fuzzing no deterministas. Si la entrada de tu prueba de fuzzing en el fork es algún parámetro que se utiliza en una llamada RPC para obtener datos (por ejemplo, consultar el balance del token de una dirección), cada ejecución de una prueba de fuzzing utiliza al menos 1 solicitud RPC, por lo que rápidamente podrías alcanzar los límites de tasa o límites de uso. Soluciones a considerar:

   - Reemplaza múltiples llamadas RPC con una sola [multicall](https://github.com/mds1/multicall).
   - Crea [semilla](/src/reference/config/testing.md#seed) especifica para  test fuzz/invariantes : esto asegura que cada ejecución de `forge test` use los mismos datos de entrada para el fuzzing. Los resultados de las RPC se almacenan en caché localmente, por lo que solo consultarás el nodo la primera vez.
   - Estructura tus pruebas de manera que los datos sobre los que estás aplicando el fuzzing sean calculados localmente por tu contrato, en lugar de datos que se utilicen en una llamada RPC (esto puede o no ser factible según lo que estés haciendo).
   - Por último, siempre puedes ejecutar un nodo local o mejorar tu plan de RPC.

1. Al escribir pruebas de forks, no utilices el identificador `--fork-url`. En su lugar, se recomienda  el siguiente enfoque por ser más flexible:

   - Define [rpc_endpoints] en el archivo de configuración foundry.toml y utiliza los [cheatcodes de forking](../forge/fork-testing.md#forking-cheatcodes).
   - Accede al endpoint de la URL RPC en tu prueba utilizando `stdChains.ChainName.rpcUrl` de `forge-std`. Consulta la lista de cadenas compatibles y los alias esperados en el archivo de configuración. [aquí](https://github.com/foundry-rs/forge-std/blob/ff4bf7db008d096ea5a657f2c20516182252a3ed/src/StdCheats.sol#L255-L271).
   - Siempre fija a un bloque para que las pruebas sean determinísticas y las respuestas de RPC se almacenen en caché.
   - Puedes encontrar más información sobre este enfoque de pruebas de forks [aquí](https://twitter.com/msolomon44/status/1564742781129502722) (esto es anterior a StdChains, por lo que ese aspecto está un poco desactualizado).

### Arnes para Tests 

#### Funciones Internas  

Para probar funciones `internal`, escribe un contrato de soporte que herede del contrato bajo prueba (CuT). Los contratos de soporte que heredan del CuT exponen las funciones `internal` como funciones `external`.

Cada función `internal` a la cual le realizamos pruebas mediante una función `external` debe seguir el siguiente `exposed_<function_name>`. Por ejemplo:

```solidity
// file: src/MyContract.sol
contract MyContract {
  function myInternalMethod() internal returns (uint) {
    return 42;
  }
}

// file: test/MyContract.t.sol
import {MyContract} from "src/MyContract.sol";

contract MyContractHarness is MyContract {
  // Deploy this contract then call this method to test `myInternalMethod`.
  function exposed_myInternalMethod() external returns (uint) {
    return myInternalMethod();
  }
}
```

#### Funciones Privadas  

Lamentablemente, aun no hay una manera correcta de realizar pruebas unitarias a métodos `private`, ya que no pueden ser accedidos por otros contratos. Las opciones incluyen:

- Convertir funciones  `private` a `internal`.
- Copiar y pegar la lógica en tu contrato de prueba y escribir un script que se ejecute en CI para asegurarse de que ambas funciones sean idénticas.

#### Funciones Perifericas

Las envolturas (harnesses) también se pueden utilizar para exponer funcionalidades o información que de otra manera no estarían disponibles en el contrato inteligente original. El ejemplo más directo es cuando queremos probar la longitud de un _array_ público. Las funciones deben seguir el patrón: `workaround_<function_name>`, como `workaround_queueLength()`.

Otro caso de uso para esto es hacer un seguimiento de datos que no seguirías en producción para ayudar a probar invariantes. Por ejemplo, podrías almacenar una lista de todos los titulares de tokens para simplificar la validación del invariante "la suma de todos los balances debe ser igual al suministro total". A menudo se conocen como "variables fantasma". Puedes obtener más información sobre esto en la charla de  [Rikard Hjort](https://twitter.com/rikardhjort) sobre [Métodos Formales para el Desarrollador DeFi en Acción](https://youtu.be/ETlNhV9jYJw)(EN).

### Buenas practicas

Gracias a la charla de [@samsczun](https://twitter.com/samczsun) sobre [¿Cómo se Escribe Código Seguro en Primer Lugar?](https://www.youtube.com/watch?v=Wm3t8Fuiy1E)(EN) es que recopilamos lso consejos y anotaciones presentes tanto  en esta como en la proxima seccion.

- No optimices para la cobertura, optimiza para pruebas bien pensadas.
- Escribe pruebas unitarias positivas y negativas.
   - Escribe pruebas unitarias _positivas_ para las cosas que el código debería manejar. Valida _todo_ el estado que cambia a partir de estas pruebas.
   - Escribe pruebas unitarias _negativas_ para las cosas que el código _no debería_ manejar. Es útil seguir con una prueba positiva (como una prueba adyacente) y realizar el cambio necesario para que pase.
   - Cada camino del código debería tener su propia prueba unitaria.
- Escribe pruebas de integración para probar características completas.
- Escribe pruebas de forks para verificar el comportamiento correcto con contratos ya desplegados existentes.

### Análisis de Contaminación

Al hacer pruebas, debes priorizar las funciones que un atacante puede afectar, es decir, las funciones que aceptan algún tipo de entrada del usuario. Estas se llaman _fuentes_.

Considera que los datos de entrada están _contaminados_ hasta que sean verificados por el código, momento en el que se consideran _limpios_.

Un _sink_ es una parte del código donde ocurre alguna operación importante. Por ejemplo, en MakerDAO eso podría ser `vat.sol`.

Debes _asegurarte_ de que nunca lleguen datos _contaminados_ a un _sink_. Esto significa que todos los datos que llegan al _sink_ deberían haber sido verificados por ti en algún momento. Por lo tanto, necesitas definir cómo deberían ser los datos y luego asegurarte de que tus verificaciones _garanticen_ que los datos sean como esperas que sean.

## Scripts

1. Mantené `run` como la función predeterminado para mayor claridad.

1. . Cualquier método que no esté destinado a ser llamado directamente en el script debe ser `internal` o `private`. En general, el único método público debería ser `run`, ya que es más fácil de leer y entender cuando cada archivo de script hace una sola cosa.

1. Considere anteponer a los scripts un número según el orden en el que deben ejecutarse durante el ciclo de vida del protocolo. Por ejemplo, `01_Deploy.s.sol`, `02_TransferOwnership.s.sol`. Esto hace que las cosas se vean  con mayor claridad . Es posible que esto no siempre se aplique dependiendo de su proyecto.

1. Pruebe sus _scripts_.

   - Pruébelos unitariamente como lo haría con contratos normales, escribiendo pruebas que afirmen los cambios de estado realizados al ejecutar el script.
   - Escriba su secuencia de comandos de implementación y pruebas de andamiaje ejecutando esa secuencia de comandos. Luego, ejecute todas las pruebas con el estado resultante de su secuencia de comandos de implementación de producción. Esta es una excelente manera de ganar confianza en un script de implementación.
   - Dentro de su script, use `require` declaraciones (o el  `if (condition) revert()` patrón si lo prefiere) para detener la ejecución de su script si algo anda mal. Por ejemplo,  `require(computedAddress == deployedAddress, "address mismatch")`. En su lugar , utilizar los `assertEq`  no detendrá la ejecución.

1. **Audite cuidadosamente qué transacciones se transmiten**. Las transacciones que no se transmiten aún se ejecutan en el contexto de una prueba, por lo que las transmisiones faltantes o las transmisiones adicionales son fuentes fáciles de error en el paso anterior.

1. **Cuidado con los que van al frente**. Forge simula su script, genera datos de transacciones a partir de los resultados de la simulación y luego transmite las transacciones. Asegúrese de que su secuencia de comandos sea sólida contra los cambios de estado de la cadena entre la simulación y la transmisión. A continuación se muestra un script de muestra vulnerable a esto:

```solidity
// Pseudo-code, may not compile.
contract VulnerableScript is Script {
   function run() public {
      vm.startBroadcast();

      // Transaction 1: Deploy a new Gnosis Safe with CREATE.
      // Because we're using CREATE instead of CREATE2, the address of the new
      // Safe is a function of the nonce of the gnosisSafeProxyFactory.
      address mySafe = gnosisSafeProxyFactory.createProxy(singleton, data);

      // Transaction 2: Send tokens to the new Safe.
      // We know the address of mySafe is a function of the nonce of the
      // gnosisSafeProxyFactory. If someone else deploys a Gnosis Safe between
      // the simulation and broadcast, the address of mySafe will be different,
      // and this script will send 1000 DAI to the other person's Safe. In this
      // case, we can protect ourselves from this by using CREATE2 instead of
      // CREATE, but every situation may have different solutions.
      dai.transfer(mySafe, 1000e18);

      vm.stopBroadcast();
   }
}
```

1. Para scripts que leen archivos de entrada JSON, coloque los archivos de entrada en formato `script/input/<chainID>/<description>.json`. Luego, tenga  `run(string memory input)` (o tome varias entradas de cadena si necesita leer varios archivos) como firma del script y utilice el método siguiente para leer el archivo JSON.

```solidity
function readInput(string memory input) internal returns (string memory) {
  string memory inputDir = string.concat(vm.projectRoot(), "/script/input/");
  string memory chainDir = string.concat(vm.toString(block.chainid), "/");
  string memory file = string.concat(input, ".json");
  return vm.readFile(string.concat(inputDir, chainDir, file));
}
```

## Comentarios

1. Para variables y métodos públicos o externos, utilice los comentarios [NatSpec](https://docs.soliditylang.org/en/latest/natspec-format.html).

   - `forge doc` los analizará para generar automáticamente la documentación.
   - Etherscan los mostrará en la interfaz de usuario del contrato.

1. Para comentarios simples de NatSpec, considere documentar los parámetros en la cadena de documentación, como`` /// @notice Returns the sum of `x` and `y`. ``, en lugar de usar las etiquetas `@param`.

1. Para comentarios NatSpec complejos, considere usar una herramienta como [PlantUML](https://plantuml.com/ascii-art) para generar diagramas visuales de ASCII para ayudar a explicar aspectos complejos del código base.

1. Cualquier comentario hecho como _markdown_ , se trasladará correctamente a la documentacion al generarla con `forge doc`, así que estructure sus comentarios como _markdown's_  cuando sea apropiado.

   - Correcto: `` /// @notice Returns the sum of `x` and `y`. ``
   - Incorrecto: `/// @notice Returns the sum of x and y.`

## Recursos

Escriba código más seguro y mejores pruebas:

- [transmissions11/solcurity](https://github.com/transmissions11/solcurity)
- [nascentxyz/simple-security-toolkit](https://github.com/nascentxyz/simple-security-toolkit)

Foundry  en acción:

- [awesome-foundry](https://github.com/crisgarner/awesome-foundry): Una lista increible de recursos para acelerar y mejorar su desarrollo con Foundry.
- [Nomad Monorepo](https://github.com/nomad-xyz/monorepo): Todos los paquetes de `contracts-*`. Aquí se encuentran ejemplos de como usar de forma apropiada muchas de las capacidades de Foundry, como puede ser el caso de _fuzzing_, de `ffi` y algunos de los _cheatcodes_.
- [Sablier V2 Core](https://github.com/sablier-labs/v2-core): Otro ejemplo del uso correcto de muchas funciones de Foundry. También es pionero en el enfoque de prueba de árbol de estados; consulte los archivos `*.tree`.
- [Uniswap Periphery](https://github.com/gakonst/v3-periphery-foundry): Ejemplo correcto del uso de la herencia aislada para _suites_ de prueba.
- [PRBMath](https://github.com/PaulRBerg/prb-math):Una biblioteca para aritmética de coma fija en Solidity, con muchas pruebas parametrizadas que aprovechan Foundry.
