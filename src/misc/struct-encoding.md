## Codificación del "Struct"

Los "Structs" son un tipo de objeto, el cual es altamente configurable, los cuales los define el usuario. En caso de que el lector tenga cierta familiaridad con otros lenguajes de programación, estos cuerpos son comparables, en cierta medida, a las clases en JS o de Python. Eso si, estos objetos se limitan pura y exclusivamente a guardar información:

```solidity
struct MyStruct {
    address addr;
    uint256 amount;
}
```

Solo el nuevo [ABI coder v2](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#abi-coder-pragma) puede codificar y decodificar matrices y estructuras anidadas arbitrariamente. Desde la salida 0.8.0 está activado de forma predeterminada, para aplicarlo a versiones anteriores, debe activarse a través de `pragma experimental ABIEncoderV2`.


Dento del ABI (Application Binary Interface), los "Struct" se asignan como si fueran del tipo "tupla". Para obtener más información sobre cómo se asignan los tipos de Solidity a los tipos ABI, consulte [Asignación de Solidity a tipos ABI](https://docs.soliditylang.org/en/latest/abi-spec.html#mapping-solidity-to-abi-types) en la documentación de Solidity.


Por tanto, los "structs" se codifican y decodifican como tuplas. Entonces, la estructura que definimos anteriormente, `MyStruct`, se asigna a la tupla` (address, uint256) `en términos de ABI.


Veamos cómo funciona esto en un contrato:

```solidity
pragma solidity =0.8.15;


contract Test {
    struct MyStruct {
        address addr;
        uint256 amount;
    }
    function f(MyStruct memory t) public pure {}
}
```

El ABI de la función `f` en este contrato es:

```json
{
	"inputs": [
		{
			"components": [
				{
					"internalType": "address",
					"name": "addr",
					"type": "address"
				},
				{
					"internalType": "uint256",
					"name": "amount",
					"type": "uint256"
				}
			],
			"internalType": "struct Test.MyStruct",
			"name": "t",
			"type": "tuple"
		}
	],
	"name": "f",
	"outputs": [],
	"stateMutability": "pure",
	"type": "function"
}
```
lo que se interpreta como: La función `f` toma un parametro del tipo `tupla`, el cual debe contener dos componentes, uno del tipo `address` y otro del tipo `uint256`.
