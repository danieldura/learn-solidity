# Capítulo 9: Victorias y Derrotas Zombie
Para nuestro juego zombie querremos contabilizar cuantas batallas han ganado o perdido nuestros zombies. De esta manera podemos mantener un "tabla de posiciones zombie" en nuestro estado de juego.

Podríamos almacenar estos datos de diferentes maneras en nuestro DApp — como mapeos individuales, como Estructuras de la tabla de posiciones o en la misma estructura Zombie.

Cada uno tiene sus propios beneficios y riesgos dependiendo en cómo planeamos interactuar con los datos. En este tutorial vamos a almacenar las estadísticas en nuestra estructura Zombie para mayor sencillez y las llamaremos winCount y lossCount.

Así que regresemos a zombiefactory.sol y añadamos estas propiedades a nuestra estructura Zombie.

# Vamos a probarlo
1. Modifique nuestra estructura Zombie para que tenga 2 propiedades más:

1. a. winCount, un uint16

1. b. lossCount, también es un uint16

Nota: Recuerde, ya que podemos insertar uints dentro de las estructuras, querremos utilizar los uints más pequeños que podamos. Un uint8 es muy pequeño, dado que 2^8 = 256 — si nuestros zombies son atacados una vez al día, podrían rebasar esto en un año. Pero 2^16 es 65536 — así que, a menos que un usuario gane o pierda todos los días por 179 años seguidos, estamos seguros.

2. Ahora que tenemos nuevas propiedades en nuestra estructura Zombie, necesitamos cambiar nuestra definición de función en _createZombie().

Cambie la definición de creación de zombie para generar cada zombie nuevo con 0 victorias y 0 derrotas.

```

pragma solidity ^0.4.19;

import "./ownable.sol";

contract ZombieFactory is Ownable {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;
    uint cooldownTime = 1 days;

    struct Zombie {
      string name;
      uint dna;
      uint32 level;
      uint32 readyTime;
      // 1. Add new properties here
      uint16 winCount;
      uint16 lossCount;
    }

    Zombie[] public zombies;

    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;

    function _createZombie(string _name, uint _dna) internal {
        // 2. Modify new zombie creation here:
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
