## Visión general de Chisel

Chisel es un REPL avanzado, el cual viene incluido en Foundry. Se puede usar para hacer test rápidos sobre el comportamiento de algun fragmentos de Solidity tanto en una red local como en un fork de red.

Chisel es parte de la suite de Foundry y viene instalada junto a  `forge`, `cast`, y `anvil`. Si aún no has instalado Foundry, cunsulta [Instalación de Foundry](../getting-started/installation.md). 

> Nota: Si tenés una versión más antigua de Foundry instalada, necesitás volver a instalar `foundryup` para que Chisel se descargue.

### Cómo usar Chisel

Para usar Anvil, simplemente escribí  `chisel`. A partir de eso, podés comenzar escribiendo líneas de Solidity en la consola! Chisel te devuelve una versión muy verbosa acompañada de un feedback con cada input.

Chisel puede ser usado tanto dentro de un proyecto de Foundry, como también fuera de uno. Si el archivo binario es ejecutado dentro de un proyecto de foundry, Chisel va a heredar toda las opciones de configuración del proyecto.

Para ver todos los comandos incluidos, escribí en el REPL `!help`.

> 📚 **Referencia**
>
> Para obtener más información ,consulta la [Referencia de `chisel` ](../reference/chisel/)donde se detalla en profundidad las capacidades de Anvil.

