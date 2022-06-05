# Capítulo 8: ¡De Vuelta al Ataque!
Suficiente refactorización — regresemos a zombieattack.sol.

Vamos a continuar definiendo nuestra función attack, ahora que tenemos el modificador ownerOf para ser utilizado.


# Vamos a probarlo

1. Añada el modificador ownerOf a attack para asegurar que el que llame posea _zombieId.

2. Lo primero que nuestra función debería hacer es conseguirles un puntero de storage a ambos zombies para poder interactuar con ellos más facilmente:

a. Anuncie un Zombie storage llamado myZombie y configurelo como igual a zombies[_zombieId].

b. Anuncie un Zombie storage llamado enemyZombie y configurelo como igual a zombies[_targetId].

3. Vamos a utilizar un número aleatorio entre 0 y 99 para determinar el resultado de nuestra batalla. Entonces anuncie un uint llamado rand y configurelo para que sea igual al resultado de la función randMod con 100 como un argumento.


```
import "./zombiehelper.sol";

contract ZombieBattle is ZombieHelper {
  uint randNonce = 0;
  uint attackVictoryProbability = 70;

  function randMod(uint _modulus) internal returns(uint) {
    randNonce++;
    return uint(keccak256(now, msg.sender, randNonce)) % _modulus;
  }

  // 1. Add modifier here
  function attack(uint _zombieId, uint _targetId) external ownerOf(_zombieId){
    // 2. Start function definition here
    Zombie storage myZombie = zombies[_zombieId];
    Zombie storage enemyZombie = zombies[_targetId];
    uint rand = randMod(100);
  }
}

```