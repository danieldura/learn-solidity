
# Capítulo 6: Refactorizando Lógica Común
Quien sea que llame a nuestra función attack — queremos asegurarnos de que el usuario sea realmente el dueño del zombie con el que está atacando ¡Sería un problema de seguridad si se pudiera atacar con el zombie de alguien más!

¿Puede pensar en cómo podríamos añadir un chequeo para ver si la persona que llama a esta función es el dueño del _zombieId que está pasando?

Pienselo por un rato, vea si puedes conseguir una respuesta tu mismo por su lado.

Tomate un momento... Revise el código de algunas de las lecciones anteriores para conseguir ideas...

Más abajo la respuesta, no continúe hasta que lo haya pensado realmente.

# La respuesta
Hemos hecho este chequeo varias veces en lecciones anteriores. En changeName(), changeDna(), y feedAndMultiply(), utilizamos el siguiente chequeo:

`require(msg.sender == zombieToOwner[_zombieId]);`

Esta es la misma lógica que necesitaremos para nuestra función attack. Ya que estamos utilizando multiples veces la misma lógica, movamos esto a su propio modifier para limpiar nuestro código y evitar redundancias.

# Vamos a probarlo
Regresamos a zombiefeeding.sol, dado que este es el primer lugar dónde usamos esa lógica. Vamos a refactorizarlo en su propio modifier.

1. Cree un modifier llamado ownerOf. Tomará 1 argumento, _zombieId (un uint).
El cuerpo debería require (requerir) que msg.sender sea igual a zombieToOwner[_zombieId] y luego continuar con la función. Puede consultar zombiehelper.sol si no recuerda la sintaxis para un modificador.

2. Cambie la definición de función de feedAndMultiply de manera que utilice el modificador ownerOf.

3. Ahora que estamos utilizando un modifier, puede eliminar la línea require(msg.sender == zombieToOwner[_zombieId]);


```
pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  KittyInterface kittyContract;

  // 1. Create modifier here
  modifier ownerOf(uint _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    _;
  }

  function setKittyContractAddress(address _address) external onlyOwner {
    kittyContract = KittyInterface(_address);
  }

  function _triggerCooldown(Zombie storage _zombie) internal {
    _zombie.readyTime = uint32(now + cooldownTime);
  }

  function _isReady(Zombie storage _zombie) internal view returns (bool) {
      return (_zombie.readyTime <= now);
  }

  // 2. Add modifier to function definition:
  function feedAndMultiply(uint _zombieId, uint _targetDna, string _species) internal ownerOf(_zombieId) {
    // 3. Remove this line
    Zombie storage myZombie = zombies[_zombieId];
    require(_isReady(myZombie));
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(_species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
    _triggerCooldown(myZombie);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }
}

```