## Análisis Estático 

### Slither

Teste su proyecto usando [slither](https://github.com/crytic/slither), acá encontramos  un ejemplo simple de `slither.config.json`:

```json
{
  "filter_paths": "lib"
}
```
Para ejecutar Slither en la totalidad del proyecto, use el siguiente comando:

```sh
slither .
```
No es necesario que se provean los `remappings` a traves de la opcion `solc_remaps`, esto se debe a que Slither es capaz de detectar de forma automatica los `remappings` en los proyectos de Foundry. Slither ejecuta el comando `forge` para montar el proyecto.

No obstante, si lo que el lector busca es hacer un análisis más específico, como podría ser el caso de un archivo específico `.sol`, ahí este debe proveer los `remappings`:

```json
{
  "solc_remaps": [
    "ds-test/=lib/ds-test/src/",
    "forge-std/=lib/forge-std/src/"
  ]
}
```
También es importante mantener el compilador `solc` actualizado así coincide con la versión utilizada por Forge con `solc-select`:

```sh
pip3 install slither-analyzer
pip3 install solc-select
solc-select install 0.8.13
solc-select use 0.8.13
slither .
```


Para obtener más información, visite la [wiki de Slither](https://github.com/crytic/slither/wiki/Usage).

Para que el lector pueda utilizar una configuración personalizada, como puede ser el caso del ejemplo `slither.config.json` que se menciona arriba, el siguiente comando se usa como se especifica en la [slither-wiki](https://github.com/crytic/slither/wiki/Usage#configuration-file). Por defecto, slither busca el archivo `slither.config.json` pero usted como usuario puede definir un `path` diferente y otorgarle a otro archivo `json` la responsabilidad de guardar la configuración :

```sh
slither --config-file <path>/file.config.json .
```

Example output (Raw):

```bash 
Pragma version^0.8.13 (Counter.sol#2) necessitates a version too recent to be trusted. Consider deploying with 0.6.12/0.7.6/0.8.7
solc-0.8.13 is not recommended for deployment
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-versions-of-solidity

setNumber(uint256) should be declared external:
        - Counter.setNumber(uint256) (Counter.sol#7-9)
increment() should be declared external:
        - Counter.increment() (Counter.sol#11-13)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#public-function-that-could-be-declared-external
Counter.sol analyzed (1 contracts with 78 detectors), 4 result(s) found
```

Slither también tiene [GitHub Action](https://github.com/marketplace/actions/slither-action) para CI/CD.

### Mythril

Para crear test de su proyecto usando [mythril](https://github.com/ConsenSys/mythril), aquí le dejamos un ejemplo `mythril.config.json`:

```json
{
  "remappings": [
    "ds-test/=lib/ds-test/src/",
    "forge-std/=lib/forge-std/src/"
  ],
  "optimizer": {
    "enabled": true,
    "runs": 200
  }
}
```

Nota, el lector debe cambiar el compilador `rustc` a `nightly` para instalar `mythril`:

```ignore
rustup default nightly
pip3 install mythril
myth analyze src/Contract.sol --solc-json mythril.config.json
```

Para obtener más información, visite la [documentación de mythril](https://mythril-classic.readthedocs.io/en/develop/).

Una cualidad interesante de `mythril` es que el lector puede pasar la versión del compilador Solc, esto se logra usando `--solc-json`. Un ejemplo de esto puede ser el siguiente:

```bash
$ myth analyze src/Counter.sol --solc-json mythril.config.json
.
.
mythril.laser.plugin.loader [INFO]: Loading laser plugin: coverage
mythril.laser.plugin.loader [INFO]: Loading laser plugin: mutation-pruner
.
.
Achieved 11.56% coverage for code: 608060405234801561001057600080fd5b5060f78061001f6000396000f3fe6080604052348015600f57600080fd5b5060043610603c5760003560e01c80633fb5c1cb1460415780638381f58a146053578063d09de08a14606d575b600080fd5b6051604c3660046083565b600055565b005b605b60005481565b60405190815260200160405180910390f35b6051600080549080607c83609b565b9190505550565b600060208284031215609457600080fd5b5035919050565b60006001820160ba57634e487b7160e01b600052601160045260246000fd5b506001019056fea2646970667358221220659fce8aadca285da9206b61f95de294d3958c409cc3011ded856f421885867464736f6c63430008100033
mythril.laser.plugin.plugins.coverage.coverage_plugin [INFO]: Achieved 90.13% coverage for code: 6080604052348015600f57600080fd5b5060043610603c5760003560e01c80633fb5c1cb1460415780638381f58a146053578063d09de08a14606d575b600080fd5b6051604c3660046083565b600055565b005b605b60005481565b60405190815260200160405180910390f35b6051600080549080607c83609b565b9190505550565b600060208284031215609457600080fd5b5035919050565b60006001820160ba57634e487b7160e01b600052601160045260246000fd5b506001019056fea2646970667358221220659fce8aadca285da9206b61f95de294d3958c409cc3011ded856f421885867464736f6c63430008100033
mythril.laser.plugin.plugins.instruction_profiler [INFO]: Total: 1.0892839431762695 s
[ADD         ]   0.9974 %,  nr      9,  total   0.0109 s,  avg   0.0012 s,  min   0.0011 s,  max   0.0013 s
.
.
[SWAP1       ]   1.8446 %,  nr     18,  total   0.0201 s,  avg   0.0011 s,  min   0.0010 s,  max   0.0013 s
[SWAP2       ]   0.8858 %,  nr      9,  total   0.0096 s,  avg   0.0011 s,  min   0.0010 s,  max   0.0011 s

mythril.analysis.security [INFO]: Starting analysis
mythril.mythril.mythril_analyzer [INFO]: Solver statistics: 
Query count: 61 
Solver time: 3.6820807456970215
The analysis was completed successfully. No issues were detected.
```
En caso de que los haya,todos los hallazgos realizados por dicha herramienta van a estar en una lista al final del `output`. Como el contrato por defecto, `Counter.sol` no tiene ninguna lógica adherida, `mythx` reporta que no es posible encontrar porblemas en el código.
