## Integrando con VSCode

Es posible obtener soporte para  Visual Studio Code instalando la [VSCode Solidity extension de JuanBlanco](https://github.com/juanfranblanco/vscode-solidity) o la [Solidity extension de Nomic](https://github.com/NomicFoundation/hardhat-vscode).

Para lograr una integración simbiótica con Foundry, es posible que el lector deba hacer ciertas modificaciones.

### 1. Remappings

Lo recomendable es que el lector coloque sus `remappings` en  `remappings.txt`.

En el caso de que estos ya estén en `foundry.toml`, el lector deberá hacer una copia de estos y moverlos a`remappings.txt`. Si el lector usa los `remappings` que se generan automaticamente en  Foundry, ejecute el comando `forge remappings > remappings.txt`.

### 2. Dependencias

Es posible que el lector desee agregar lo siguiente a su archivo `.vscode/settings.json` para que la extensión que se haya elegido sea identificada por el compilador:

```json
{
  "solidity.packageDefaultDependenciesContractsDirectory": "src",
  "solidity.packageDefaultDependenciesDirectory": "lib"
}
```

Donde `src` es la carpeta en la cual encontramos el código base y  `lib` es la carpeta donde encontramos las dependencias.

### 3. Formatter

To enable the built-in formatter that comes with Foundry to automatically format your code on save, you can add the following settings to your `.vscode/settings.json`:

Para habilitar el Formatter integrado con Foundry para formatear de forma automática su código al guardarlo, puede agregar las siguientes configuraciones a su `.vscode/settings.json`:

```json
{
  "editor.formatOnSave": true,
  "[solidity]": {
    "editor.defaultFormatter": "JuanBlanco.solidity" 
  },
  "solidity.formatter": "forge",
}
```
Para configurar alguno de los parametros del `formatter`, dirigase a la sección de [Formatter](../reference/config/formatter.md).

### 4. Versión de Solc 

Por ultimo, es recomendable especificar que versión del compilador de Solidity se desea utilizar:

```json
"solidity.compileUsingRemoteVersion": "v0.8.17"
```
Para poder alinear Foundry con la versión elegida, agregue lo siguiente a su perfil `default` en  `foundry.toml`.

```toml
solc = "0.8.17"
```
