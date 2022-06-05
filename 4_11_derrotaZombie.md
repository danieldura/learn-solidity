# Capítulo 11: Derrota Zombie 😞
Ahora que hemos codificado lo que ocurre cuando su zombie gana, descifremos lo que ocurre cuando pierde.

En nuestro juego, cuando los zombies pierden su nivel no disminuye — simplemente añaden una derrota a su lossCount y su tiempo de enfriamiento o recarga se activa para que tengan que esperar un día antes de volver a atacar.

Para implementar está lógica necesitaremos un `statement else`.

Los statements else se escriben como en JavaScript y muchos otros lenguajes:

if (zombieCoins[msg.sender] > 100000000) {
  // You rich!!!
} else {
  // We require more ZombieCoins...
}
Vamos a probarlo
Añada un statement else. Si nuestro zombie pierde:

a. Incrementa el lossCount de myZombie.

b. Incrementa el winCount del enemyZombie.

Fuera del statement "else", ejecute la función _triggerCooldown en myZombie. De esta manera el zombie sólo puede atacar una vez al día.

```
import "./zombiehelper.sol";

contract ZombieBattle is ZombieHelper {
  uint randNonce = 0;
  uint attackVictoryProbability = 70;

  function randMod(uint _modulus) internal returns(uint) {
    randNonce++;
    return uint(keccak256(now, msg.sender, randNonce)) % _modulus;
  }

  function attack(uint _zombieId, uint _targetId) external ownerOf(_zombieId) {
    Zombie storage myZombie = zombies[_zombieId];
    Zombie storage enemyZombie = zombies[_targetId];
    uint rand = randMod(100);
    if (rand <= attackVictoryProbability) {
      myZombie.winCount++;
      myZombie.level++;
      enemyZombie.lossCount++;
      feedAndMultiply(_zombieId, enemyZombie.dna, "zombie");
    } else {
      myZombie.lossCount++;
      enemyZombie.winCount++;
    }
    _triggerCooldown(myZombie);
  }
}


```