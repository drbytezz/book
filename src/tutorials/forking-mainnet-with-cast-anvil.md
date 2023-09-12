## _Forks_ de _Mainnet_ usando Cast y Anvil

### Introducción

AL combinar[Anvil][anvil] y [Cast][cast],se puede realizar _forks_ y hacer pruebas interactuando con contratos en una red real. El objetivo de este tutorial es mostrarle cómo transferir tokens Dai de alguien que posee Dai a una cuenta creada por Anvil.

### Set Up

Comencemos con un _fork_ de la red principal.

```sh
anvil --fork-url https://mainnet.infura.io/v3/$INFURA_KEY
```

Verás que se crean 10 cuentas con sus claves públicas y privadas. Nosotros trabajaremos con `0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266` (Llamemosle  Martina).

### Transferir dai

Vaya a Etherscan y busque alquien que posea tokens Dai ([here](https://etherscan.io/token/0x6b175474e89094c44da98b954eedeac495271d0f#balances)). Escojamos una cuenta al azar. En este ejemplo estaremos utilizando`0xad0135af20fa82e106607257143d0060a7eb5cbf`. Exportemos nuestros contratos y cuentas como variables de entorno (`.env`):

```sh
export MARTI=0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
export DAI=0x6b175474e89094c44da98b954eedeac495271d0f
export LUCKY_USER=0xad0135af20fa82e106607257143d0060a7eb5cbf
```

Revisemos el balance de Martina usando [`cast call`][cast-call]:

```sh
$ cast call $DAI \
  "balanceOf(address)(uint256)" \
  $MARTI
0
```

Del mismo modo, también podemos consultar el balance de nuestro afortunado usuario usando `cast call`:

```sh
$ cast call $DAI \
  "balanceOf(address)(uint256)" \
  $LUCKY_USER
71686045944718512103110072
```

Hagamos una transferencia desde del usuario afortunado a Martina usando [`cast send`][cast-send]:

```sh
# This calls Anvil and lets us impersonate our lucky user
$ cast rpc anvil_impersonateAccount $LUCKY_USER
$ cast send $DAI \
--unlocked \
--from $LUCKY_USER \
  "transfer(address,uint256)(bool)" \
  $MARTI \
  1686045944718512103110072
blockHash               0xbf31c45f6935a0714bb4f709b5e3850ab0cc2f8bffe895fefb653d154e0aa062
blockNumber             15052891
...
```

Comprobemos que la transferencia funcionó:

```sh
cast call $DAI \
  "balanceOf(address)(uint256)" \
  $MARTI
1686045944718512103110072

$ cast call $DAI \
  "balanceOf(address)(uint256)" \
  $LUCKY_USER
70000000000000000000000000
```

[anvil]: ../reference/anvil/
[cast]: ../reference/cast/
[cast-call]: ../reference/cast/cast-call.md
[cast-send]: ../reference/cast/cast-send.md
