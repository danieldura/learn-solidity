# Capítulo 9: Preventing Overflows

¡Felicidades, eso completa nuestra implementación de ERC721!

Eso no fue tan difícil, ¿verdad? Muchas de estas cosas de Ethereum suenan realmente complicadas cuando escuchas a personas hablar de ellas, así que la mejor manera de entenderlas es realizarlas tú mismo.

Tenga en cuenta que esto es sólo una implementación mínima. Hay características adicionales que podemos desear añadir a nuestra implementación, como algunas comprobaciones adicionales para asegurarnos de que los usuarios no transfieran accidentalmente sus zombies a la dirección 0 (que se llama "quemar" un token, básicamente se envía a una dirección a la cual nadie tiene su clave privada, haciéndolo irrecuperable). Quizás, implementar una lógica básica de subasta en su DApp. (¿Puedes pensar en algunas formas para implementar eso?).

Pero queríamos que esta lección fuera manejable, por lo que optamos por la implementación más básica. Si desea ver un ejemplo de una implementación más profunda, puede echar un vistazo al contrato de OpenZeppelin con ERC721 después de este tutorial.

# Mejoras de seguridad en el contrato: Desbordamientos por exceso (Overflows) y por defecto (Underflows)

Vamos a ver una característica de seguridad importante que debe tener en cuenta al escribir contratos inteligentes: Prevención de desbordamientos por exceso (overflow) y por defecto (underflow).

¿Qué es un desbordamiento?

Digamos que tenemos un campo uint8, que sólo puede tener hasta 8 bits. Esto significa, que el número en binario más grande posible sería 11111111 (o en decimal, 2^8 - 1 = 255).

Eche un vistazo al código siguiente. ¿Qué valor tendrá number al final?



```s
uint8 number = 255;
number++;

```


En este caso, hemos provocado un desbordamiento por exceso (overflow) — así que number convertirá su valor igual a 0 aunque lo hayamos aumentado. (Si añades +1 al valor binario 11111111, se reiniciará al valor inicial 00000000, igual que un reloj pasa de las 23:59 a las 00:00).

Un desbordamiento por defecto es similar, cuando intentemos restar un 1 a un campo uint8 que tiene valor 0, ahora pasará a valer 255 (porque el tipo uint no tiene signo y por lo tanto, no puede ser negativo).

Si bien no estamos usando uint8 aquí, y parece poco probable que uint256 se desborde aumentanto de 1 en 1 cada vez (2^256 es realmente un número enorme), sigue siendo bueno poner protecciones en nuestro contrato para que nuestro DApp nunca tenga un comportamiento inesperado en el futuro.

# Usando SafeMath
Para prevenir esto, OpenZeppelin ha creado una librería llamada SafeMath que previene estos problemas por defecto.

Pero antes de entrar en eso... ¿Qué es una librería?

Una librería es un tipo de contrato especial en Solidity. Una de las cosas para las cuales es útil, es para asociar funciones a tipos de datos nativos.

Por ejemplo, con la librería SafeMath, usaremos la sintaxis using SafeMath for uint256. La librería SafeMath tiene 4 funciones— add, sub, mul y div. Y ahora podemos acceder a estas funciones desde uint256 de la siguiente manera:

```s
using SafeMath for uint256;

uint256 a = 5;
uint256 b = a.add(3); // 5 + 3 = 8
uint256 c = a.mul(2); // 5 * 2 = 10
```

Veremos qué hacen estas funciones en el próximo capítulo, pero por ahora agreguemos la librería SafeMath a nuestro contrato.

# Vamos a probarlo
Ya hemos incluido la librería SafeMath de OpenZeppelin por ti en safemath.sol. Ahora puedes echarle un vistazo rápido al código si lo deseas, pero lo veremos en profundidad en el siguiente capítulo.

Primero, declaremos en nuestro contrato que se use SafeMath. Haremos esto en nuestra ZombieFactory, nuestro contrato base — de esa manera podemos usarlo en cualquiera de los subcontratos que hereden de este.

Importa safemath.sol en zombiefactory.sol.

Añada la declaración using SafeMath for uint256;.



```s

pragma solidity ^0.4.19;

import "./ownable.sol";
// 1. Import here
import "./safemath.sol";

contract ZombieFactory is Ownable {

  // 2. Declare using safemath here
  using SafeMath for uint256;

  event NewZombie(uint zombieId, string name, uint dna);

  uint dnaDigits = 16;
  uint dnaModulus = 10 ** dnaDigits;
  uint cooldownTime = 1 days;

  struct Zombie {
    string name;
    uint dna;
    uint32 level;
    uint32 readyTime;
    uint16 winCount;
    uint16 lossCount;
  }

  Zombie[] public zombies;

  mapping (uint => address) public zombieToOwner;
  mapping (address => uint) ownerZombieCount;

  function _createZombie(string _name, uint _dna) internal {
    uint id = zombies.push(Zombie(_name, _dna, 1, uint32(now + cooldownTime), 0, 0)) - 1;
    zombieToOwner[id] = msg.sender;
    ownerZombieCount[msg.sender]++;
    NewZombie(id, _name, _dna);
  }

  function _generateRandomDna(string _str) private view returns (uint) {
    uint rand = uint(keccak256(_str));
    return rand % dnaModulus;
  }

  function createRandomZombie(string _name) public {
    require(ownerZombieCount[msg.sender] == 0);
    uint randDna = _generateRandomDna(_name);
    randDna = randDna - randDna % 100;
    _createZombie(_name, randDna);
  }

}
```