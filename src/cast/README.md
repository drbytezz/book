## Visión general de Cast

Cast es la herramienta de línea de comandos de Foundry para realizar llamadas RPC de Ethereum. Podes hacer llamadas a contratos inteligentes, enviar transacciones o recuperar cualquier tipo de datos de la cadena, ¡todo desde tu línea de comandos


### Cómo usar Cast

Para usar cast, simplemente escribí el comando  [`cast`](../reference/cast/cast.md) seguido por un sub-comando:

```bash
$ cast <subcomando>
```

#### Ejemplo

Usemos `cast` para obtener la cantidad en circulacion, o `total supply`, del token DAI :

```bash
{{#include ../output/cast/cast-call:all}}
```

`cast` también proporciona muchos subcomandos convenientes, como puede ser el caso de decodificar la informacion de calldata:

```bash
{{#include ../output/cast/cast-4byte-decode:all}}
```
Además, puedes usar `cast` para enviar mensajes arbitrarios. Aquí tienes un ejemplo de cómo enviar un mensaje entre dos cuentas de Anvil.

```bash
$ cast send --private-key <Your Private Key> 0x3c44cdddb6a900fa2b585dd299e03d12fa4293bc $(cast from-utf8 "hello world") --rpc-url http://127.0.0.1:8545/
```

<br>

> 📚 **Referencia**
>
> Para obtener más información, consulta la [Referencia de `cast`](../reference/cast/),donde se encuentra una revisión de todos los comandos y subcomandos disponibles.
