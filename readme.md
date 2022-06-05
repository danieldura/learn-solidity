# learning solidity


- Guardar cuesta gas (dinero)
- Pordefecto es inseguro: Utilizar libreria SafeMath
  - las funciones de SafeMath's de `add, sub, mul, y div` son funciones que realizan las 4 operaciones básicas de matemáticas, pero lanzan un error si ocurre un desbordamiento por exceso o por defecto.
- guardar datos en la blockchain es una de las operaciones más costosas de Solidity. Pero usar eventos es mucho más barato en términos de gas.


Palabras reservadas
- Library
- using


buenas practicas
- comentaios


Docs
- https://docs.soliditylang.org/en/latest/


Q&A
- ¿Que necesita Web3.js para poder hablar con nuestro contrato inteligente?: va a necesitar dos cosas: su address y su ABI.

- Un wei es la subunidad más pequeña del Ether — hay 10^18 weis en un ether



webs

https://hedera.com/blog/introduction-solidity-smart-contracts

- https://solidity-by-example.org

https://docs.hedera.com/guides/getting-started/try-examples/deploy-your-first-smart-contract

https://www.smartcontract.engineer

