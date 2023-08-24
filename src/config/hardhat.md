## Integración con Hardhat

Es posible tener un proyecto de Foundry que trabaje a la par con uno de[Hardhat](https://hardhat.org/). Para lograr lo ya mencionado, se asume que se tiene un proyecto de Foundry funcional y a este se le quiere agregar Hardhat. Además, asume que el usuario tenga cierta familiaridad con Hardhat.

### ¿Por qué no funciona de manera predeterminada?

Esto se debe a que Hardhat, por defecto espera que las librerias sean instaladas en la carpeta `node_modules`, siendo esta la carpeta default de todos los proyectos NodeJS dependencies. Por otra parte,  Foundry espera que dichas librerias estén bajo la carpeta `lib`. Por suerte, [podemos configurar Foundry](../reference/config/overview.md), pero presenta ciertos obstaculos cuando hablamos de compatibilizar con la estructura de `node_modules`.

Por esta razón, se recomienda el uso del [hardhat-preprocessor](https://www.npmjs.com/package/hardhat-preprocessor). Hardhat-preprocessor es, tal y como lo sugiere su nombre, un plugin de Hardhat que nos permite pre-procesar los contratos antes de que corran bajo el manto del compilador de Solidity .

Usamos esto para modificar la forma en que importamos nuestras directivas en los archivos de Solidity y así resolver las rutas absolutas a las bibliotecas según el archivo `remappings.txt` de Foundry, antes de que Hardhat intente compilarlos. Esto, por supuesto, ocurre solo en la memoria, por lo que tus archivos de Solidity reales nunca se modifican. Ahora, Hardhat está "contento" de cumplir y compilar utilizando las bibliotecas que instalaste con Foundry.

### Mostrame el repositorio de ejemplo!

[Disfruta pa!](https://github.com/foundry-rs/hardhat-foundry-template)

Si gustas de adaptar esto a un proyecto de Foundry que ya tenés o aprender cómo funciona, lee las instrucciones que siguen a continuación:

### Instrucciones

Dentro de  tu proyecto de Foundry:

1. `npm init` - Configura los detalles del proyecto, como normalmente lo haces.
2. `npm install --save-dev hardhat` - Instala Hardhat.
3. `npx hardhat` - Configura tu proyecto de Hardhat para que quede en la misma carpeta.
4. `forge remappings > remappings.txt` - Vas a necesitar ejecutar este comando cada vez que modifiques las librerias en Foundry.

Ahora bien, asegurate de hacer lso siguientes cambios en el proyecto de Hardhat.
Asumimos que tu configuracion se basa en TypeScript:

1. `npm install --save-dev hardhat-preprocessor` - [Detalles del hardhat-preprocessor](https://www.npmjs.com/package/hardhat-preprocessor)
2. Agrega `import "hardhat-preprocessor";` a tu archivo `hardhat.config.ts`.
3. Asegurate de que la siguente función este presente (podes agregarla a tu archivo `hardhat.config.ts` o en algun otro lugar e importarla - además asegurate de que `import fs from "fs";` este presente en el archivo al que se agrego la funcion):

```typescript
function getRemappings() {
  return fs
    .readFileSync("remappings.txt", "utf8")
    .split("\n")
    .filter(Boolean) // remove empty lines
    .map((line) => line.trim().split("="));
}
```

*Gracias a [@DrakeEvansV1](https://twitter.com/drakeevansv1) y [@colinnielsen](https://github.com/colinnielsen) por este fragmento*

4. Agrega lo siguiente al archivo que exportaste el objeto `HardhatUserConfig`:

```typescript
...
preprocess: {
  eachLine: (hre) => ({
    transform: (line: string) => {
      if (line.match(/^\s*import /i)) {
        for (const [from, to] of getRemappings()) {
          if (line.includes(from)) {
            line = line.replace(from, to);
            break;
          }
        }
      }
      return line;
    },
  }),
},
paths: {
  sources: "./src",
  cache: "./cache_hardhat",
},
...
```
Ahora, Hardhat debería funcionar de forma correcta con Foundry. Ahora, el lector puede realizar test y scripts tanto en Foundry como en Hardhat manteniendo el acceso a sus contratos.

### Usando Foundry en un proyecto ya existente de Hardhat

Supongamos que el lector ya tiene un proyecto en Hardhat con algunas dependencias, como puede ser el caso de `@OpenZeppelin/contracts` en la carpeta `node_modules/`. 

Podés activar Foundry para realizar los test en solo 4 pasos:

Antes de empezar, observemos las carpetas:

- Los Contratos están en `contracts`
- La unidad de test de Hardhat están en `test`, y nuestros test de Foundry se encuentran en `test/foundry`
- Hardhat guarda su cache en `cache`, y el cache resultante de Foundry lo vamos a poner en `forge-cache`

### 4 pasos para agregar los test de Foundry 

1. Copia `lib/forge-std` desde un proyecto nuevo de foundry y pegalo en esta carpeta, la cual contiene el proyecto de Hardhat. Nota: es posible hacer esto ejecutando el comando `forge init --force` para iniciar un proyecto de Foundry en esta carpeta, que no está vacía, y elimina aquellas carpetas que no sean necesarias que son creadas por Foundry init.
2. Copia siguente  configuración a tu archivo `foundry.toml` en este proyecto de Hardhat y modifica los siguientes campos`src`, `out`, `test`, `cache_path`:

```toml
[profile.default]
src = 'contracts'
out = 'out'
libs = ['node_modules', 'lib']
test = 'test/foundry'
cache_path  = 'forge-cache'

# Vease tambien mas configuración en  https://book.getfoundry.sh/reference/config.html
```

3. Crea un archivo `remappings.txt` para hacer que el proyecto de Foundry funcione de forma correcta con la VS Code Solidity extension:

```ignore
ds-test/=lib/forge-std/lib/ds-test/src/
forge-std/=lib/forge-std/src/
```

Para mas informacion sobre`remappings.txt` y VS Code Solidity extension, vease: [Dependencias del remappings](../projects/dependencies.md?#remapping-dependencies), [Integrando con VSCode](vscode.md)

4. Crea un sub-directorio llamado `test/foundry` y alli escribi los test de Foundry. 

Pongamos el archivo de test que veine como ejemplo, `Contract.t.sol` en esta carpeta y utilicemos el comando de test de Foundry 
```bash
forge test
```

Ahora, los test de Foundry deben funcionar de forma simbiotica con tu proyecto de Hardhat. Como no modificamos el proyecto de Hardhat, este debería funcionar como lo hacía antes de la integracion.

