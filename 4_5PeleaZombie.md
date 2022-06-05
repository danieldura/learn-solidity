# Capítulo 5: Pelea Zombie
Ahora que tenemos una fuente de aleatoriedad en nuestro contrato, podemos utilizar en nuestras batallas zombie para calcular el resultado.

Nuestras batallas zombie funcionarán de la siguiente manera:

Escoge a uno de tus zombies y escoge un zombie del oponente para atacar.
Si tu eres el zombie que ataca, tendrás un 70% de oportunidad de ganar. El zombie que defiende tendrá un 30% de oportunidad de ganar.
Todos los zombies (atacando y defendiendo) tendrán un *winCount* y un *lossCount* que incrementará dependiendo del resultado de la batalla.
Si el zombie que ataca gana, sube de nivel y genera un nuevo zombie.
Si pierde, no ocurre nada (solamente incrementa su lossCount)
Sea que gane o que pierda, el tiempo de enfriamiento o recarga del zombie que ataca empezará a correr.
Esto es mucha lógica a implementar así que lo haremos por partes en los siguientes capítulos.

# Ponlo a prueba
1. Dele a nuestro contrato una variable uint llamada attackVictoryProbability, y configurela como igual a 70.

Cree una función llamada attack. Tomará dos parametros: _zombieId (una uint) y _targetId (también una uint). Debería ser una función external.

Deje vacío el cuerpo de función por ahora.


import "./zombiehelper.sol";

contract ZombieBattle is ZombieHelper {
  uint randNonce = 0;
  // Create attackVictoryProbability here
  uint attackVictoryProbability = 70;

  function randMod(uint _modulus) internal returns(uint) {
    randNonce++;
    return uint(keccak256(now, msg.sender, randNonce)) % _modulus;
  }

  // Create new function here
  function attack(uint _zombieId, uint _targetId) external {

  }

}
