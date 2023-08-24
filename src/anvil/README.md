## Visión general de Anvil

Anvil es una `testnet` local que viene por defecto incluida en Foundry. Esto te permite testear contratos desde tu frontend o inclusive interactuar con otras RPCs.

Anvil es parte de la suite de Foundry y viene instalada junto a `forge`, `cast`, y `chisel`. Si aún no has instalado Foundry, cunsulta [Instalación de Foundry](../getting-started/installation.md). 

> Nota: Si tenés una versión más antigua de Foundry instalada, necesitás volver a instalar `foundryup` para que Anvil se descargue.

### Cómo usar Anvil

Para usar Anvil, simplemente escribí `anvil`. Deberías ver una lista de cuentas y claves privadas disponibles para su uso, así como la dirección y el puerto en el que el nodo está escuchando."

Anvil es altamente configurable. Puedes ejecutar `anvil -h` para ver todas las opciones de configuración.

Algunas opciones básicas son:

```bash
# Número de cuentas de desarrollo para generar y configurar. [valor por defecto: 10]
anvil -a, --accounts <CUENTAS>

# La bifurcación dura de EVM a utilizar. [valor por defecto: última]
anvil --hardfork <BIFURCACIÓN>

# Número de puerto en el que escuchar. [valor por defecto: 8545]
anvil -p, --port <PUERTO>
```

> 📚 **Referencia**
>
> Para obtener más información, consulta la [Referencia de `anvil` ](../reference/anvil/)donde se detalla en profundidad las capacidades de Anvil.

