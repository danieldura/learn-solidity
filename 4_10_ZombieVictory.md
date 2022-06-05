# Capítulo 10: Zombie Victory 😄
Ahora que tenemos un winCount y lossCount, podemos actualizarlos dependiendo de cuál zombie gane la pelea.

En el capítulo 6 calculamos un número aleatorio entre 0 y 100. Ahora utilicemos ese número para determinar quien gana la pelea y actualizar nuestras estadísticas apropiadamente.


# Vamos a probarlo
Cree una declaración `if` que verifique si rand es menor o igual a attackVictoryProbability.

Si esta condición resulta verdadera ¡Nuestro zombie gana! Entonces:

a. Incrementa el winCount de myZombie.

b. Incrementa el level de myZombie. (¡¡¡Sube de nivel!!!)

c. Incrementa el lossCount del enemyZombie. (¡¡¡Perdedor!!! 😫 😫 😫)

d. Ejecute la función feedAndMultiply. Revise zombiefeeding.sol para que vea la sintaxis para llamarlo. Para el 3er argumento (_species), pase la cadena de caracteres "zombie". (Esto no hace nada ahora mismo, pero luego podríamos añadir un poco más de funcionalidad para generar zombies tipo "zombie-based" si lo deseamos)


````
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
    // Start here
    if(rand <= attackVictoryProbability){
      myZombie.winCount++;
      myZombie.level++;
      enemyZombie.lossCount++;
      feedAndMultiply(_zombieId, enemyZombie.dna, "zombie");
    }
  }
}

```