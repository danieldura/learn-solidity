# Capítulo 11: SafeMath Part 3
¡Genial, ahora nuestra implementación de ERC721 está a salvo de desbordamientos!

Volviendo al código que escribimos en lecciones anteriores, hay algunos otros lugares en nuestro código que podrían ser vulnerables a desbordamientos.

Por ejemplo, en ZombieAttack tenemos:

```s
myZombie.winCount++;
myZombie.level++;
enemyZombie.lossCount++;
```

También deberíamos evitar los desbordamientos aquí, solo para estar seguros. (En general, es una buena idea usar SafeMath en lugar de las operaciones matemáticas básicas. Tal vez en una versión futura de Solidity estas se implementarán de manera predeterminada, pero por ahora debemos tomar precauciones de seguridad adicionales en nuestro código).

Sin embargo, tenemos un pequeño problema — winCount y lossCount son de tipo uint16, y level es de tipo uint32. Entonces, si usamos el método add de SafeMath con estos parámetros de entrada, en realidad no nos protegerá del desbordamiento ya que convertirá estos tipos en uint256:

```s
function add(uint256 a, uint256 b) internal pure returns (uint256) {
  uint256 c = a + b;
  assert(c >= a);
  return c;
}

// Si usamos `.add` con un `uint8`, lo convertirá en `uint256`.
// Por lo tanto no se desbordará, ya que 256 funciona en

```

Esto significa que vamos a tener que implementar 2 librerías más para evitar los casos con uint16 y uint32. Podemos llamarlos SafeMath16 y SafeMath32.

El código será exactamente igual al de SafeMath, excepto que todas las instancias de uint256 serán reemplazadas por uint32 o uint16 .

Hemos avanzado esta parte e implementado el código por ti — mira en safemath.sol para ver el código.

Ahora tenemos que implementarlo en ZombieFactory.

# Vamos a probarlo
Tareas:

1. Declarar que estamos usando SafeMath32 para uint32.

2. Declarar que estamos usando SafeMath16 para uint16.

Hay una línea más de código en ZombieFactory donde deberíamos usar un método de SafeMath. Hemos dejado un comentario para indicar dónde.


```s

pragma solidity ^0.4.19;

import "./ownable.sol";
import "./safemath.sol";

contract ZombieFactory is Ownable {

  using SafeMath for uint256;
  using SafeMath32 for uint32;
  using SafeMath16 for uint16; 
  // 1. Declara el uso de SafeMath32 for uint32f
  // 2. Declara el uso de SafeMath16 for uint16

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
    // Nota: Decidimos no prevenir el problema del año 2018... sin embargo no debemos
    // preocuparnos de readyTime. Igual nuestra DApp funcionará hasta el año 2038 ;)
    uint id = zombies.push(Zombie(_name, _dna, 1, uint32(now + cooldownTime), 0, 0)) - 1;
    zombieToOwner[id] = msg.sender;
    // 3. Usemos el metodo `add` de SafeMath aquí:
    ownerZombieCount[msg.sender] = ownerZombieCount[msg.sender].add(1);
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