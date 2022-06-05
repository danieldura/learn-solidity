# Capítulo 4: Refactoring
¡OH! Acabamos de introducir un error en nuestro código que hará que no compile. ¿Lo notaste?.

En el capítulo anterior definimos una función llamada ownerOf. Pero si recuerdas de la Lección 4, también creamos un modifier con el mismo nombre ownerOf, en zombiefeeding.sol.

Si intentaste compilar este código, el compilador te mostró un error al decir que no puedes tener un modifier y una función con el mismo nombre.

Entonces, ¿deberíamos simplemente cambiar el nombre de la función en ZombieOwnership por otra cosa?

No, no podemos hacer eso! Recuerde, estamos utilizando el token estándar ERC721, lo que significa que otros contratos esperarán que nuestro contrato tenga funciones con estos nombres exactos definidos. Esto es lo que hace que los estándares sean útiles — si otro contrato sabe que nuestro contrato cumple con ERC721, simplemente puede comunicarse con nosotros sin necesidad de saber nada sobre nuestras decisiones internas de la implementación.

Entonces, eso significa que tendremos que refactorizar nuestro código de la Lección 4 para cambiar el nombre del modifier por otra cosa.

# Vamos a probarlo
Volvamos a zombiefeeding.sol. Vamos a cambiar el nombre del modifier de **ownerOf** a **onlyOwnerOf**.

1. Cambie el nombre del modifier a onlyOwnerOf

2. Desplácese hacia abajo a la función feedAndMultiply, que usa este modifier . Tendremos que cambiar el nombre aquí también.

> Nota: También usamos este modifier en zombiehelper.sol y zombieattack.sol, pero para no perder demasiado tiempo de esta lección en refactorización, hemos avanzado y cambiado los nombres del modifier en esos archivos por ti.


```s
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

  // 1. Change modifier name to `onlyOwnerOf`
  modifier onlyOwnerOf(uint _zombieId) {
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

  // 2. Change modifier name here as well
  function feedAndMultiply(uint _zombieId, uint _targetDna, string _species) internal onlyOwnerOf(_zombieId) {
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