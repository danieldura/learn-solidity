Conceptos avanzados de Solidity -> Capítulo 4: Gas

¡Genial! Ahora sabemos como actualizar partes clave de la DApp mientras prevenimos que otros usuarios nos fastidien nuestros contratos.

Vamos a ver otra característica por la que Solidity es diferente a otros lenguajes de programación:

Gas — el combustible que mueven las DApps de Ethereum
En Solidity, tus usuarios tienen que pagar cada vez que ejecuten una función en tu DApp usando una divisa llamada gas. Los usuarios compran gas con Ether (la divisa de Ethereum), así que deben gastar ETH para poder ejecutar funciones en tu DApp.

La cantidad de gas necesaria para ejecutar una función depende en cuán compleja sea la lógica de la misma. Cada operación individual tiene un coste de gas basado aproximadamente en cuantos recursos computacionales se necesitarán para llevarla a cabo (p. ej. escribir en memoria es más caro que añadir dos integers). El total coste de gas de tu función es la suma del coste de cada una de sus operaciones.

Como ejecutar funciones cuestan dinero real a los usuarios, la optimización de código es mucho más importante en Ethereum que en cualquier otro lenguaje. Si tu código es descuidado, tus usuarios van a tener que pagar un premium para ejecutar tus funciones - esto puede suponer millones de dolares gastados innecesariamente por miles de usuarios en tasas.

¿Por qué es necesario el gas?
Ethereum es como un ordenador grande, lento, pero extremandamente seguro. Cuando ejecutas una función, cada uno de los nodos de la red necesita ejecutar esa misma función para comprobar su respuesta - miles de nodos verificando cada ejecución de funciones es lo que hace a Ethereum descentralizado, y que sus datos sean inmutables y resistentes a la censura.

Los creadores de Ethereum querian estar seguros de que nadie pudiese obstruir la red con un loop infinito, o acaparar todos los recursos de la red con cálculos intensos. Por eso no hicieron las transacciones gratuitas, y los usuarios tienen que pagar por su poder de computo así como por su espacio en memoria.

Nota: Esto no es necesariamente verdadero en las sidechains, así como los autores de CryptoZombies están construyendo en Loom Network. Es probable que nunca se ejecute un juego como World of Warcraft directamente en la mainnet de Ethereum - el coste de gas sería excesivamente caro. Pero puede ejecutarse en una sidechain con un algoritmo de consenso diferente. Hablaremos más sobre que tipos de DApps querrás implementar en la sidechain y cual en la mainnet de Ethereum en lecciones futuras.

Empaquetado struct para ahorrar gas
En la Lección 1, mencionamos que hay otros tipos de uint: uint8, uint16, uint32, etc.

Normalmente no hay ningún beneficio en usar cualquiera de estos subtipos porque Solidity reserva 256 bits de almacenamiento independientemente del tamaño del uint. Por ejemplo, usando uint8 en vez de uint (uint256) no te ahorrará nada de gas.

Pero hay una excepción para esto: dentro de los struct.

Si tienes varios uints dentro de una estructura, usar un uint de tamaño reducido cuando sea posible permitirá a Solidity empaquetar estas variables para que ocupen menos espacio en memoria. Por ejemplo:


struct NormalStruct {
  uint a;
  uint b;
  uint c;
}

struct MiniMe {
  uint32 a;
  uint32 b;
  uint c;
}

// `mini` costará menos gas que `normal` debido al empaquetado de la estructura
NormalStruct normal = NormalStruct(10, 20, 30);
MiniMe mini = MiniMe(10, 20, 30); 

Por esta razón, dentro de una estructura querrás usar los subtipos más pequeños que vayas a necesitar.

Querrás también agrupar los tipos de datos que sean iguales (es decir, ponerlos al lado en la estructura) así Solidity podrá minimizar el espacio requerido. Por ejemplo, una estructura con campos uint c; uint32 a; uint32 b; costará menos gas que una estructura con campos uint32 a; uint c; uint32 b; porque los campos uint32 están agrupados al lado.

Vamos a probarlo
En esta lección, vamos a añadir 2 nuevas características a nuestros zombis: level y readyTime — este último se usará para implementar un temporizador que limite la alimentación del zombi.

Volvamos de nuevo a zombiefactory.sol.

Añade dos propiedades más a la estructura de Zombie: level (un uint32), y readyTime (también un uint32). Queremos agrupar estos tipos de dato juntos, así que vamos a colocarlos al final de la estructura.
32 bits son más que suficientes para guardar el nivel del zombi y el timestamp, esto nos ahorrará algo del coste de gas agrupando los datos mejor que si usasemos el típico uint (256-bits).