
# Capítulo 1: Payable
Hasta ahora, hemos cubierto unos cuantos modificadores de función. Puede resultar difícil tratar de recordar todo, así que hagamos un breve repaso:

1. Tenemos modificadores de visibilidad que controlan desde dónde y cuándo la función puede ser llamada: private significa que sólo puede ser llamada desde otras funciones dentro del contrato; internal es como private pero también puede ser llamada por contratos que hereden desde este; external sólo puede ser llamada desde afuera del contrato; y finalmente public puede ser llamada desde cualquier lugar, tanto internamente como externamente.

2. También tenemos modificadores, los cuales nos dicen cómo interactúa la función con la BlockChain: view nos indica que al ejecutar la función, ningún dato será guardado/cambiado. pure nos indica que la función no sólo no guarda ningún dato en la blockchain, si no que tampoco lee ningún dato de la blockchain. Ambos no cuestan nada de combustible para llamar si son llamados externamente desde afuera del contrato (pero si cuestan combustible si son llamado internamente por otra función).

3. Luego tenemos los modifiers personalizados, de los cuales aprendimos en la Lección 3: onlyOwner y aboveLevel, por ejemplo. Para estos podemos definir la lógica personalizada para determinar cómo afectan a una función.

Todos estos modificadores pueden ser apilados juntos en una definición de función de la siguiente manera:

function test() external view onlyOwner anotherModifier { /* ... */ }
En este capítulo, vamos a presentar un modificador de función más: payable.


# El Modificador payable
Las funciones payable son parte de lo que hace de Solidity y Ethereum algo tan genial — son un tipo de función especial que pueden recibir Ether.

Piensalo por un momento. Cuando llama una función API en un servidor web normal, no puede enviar dólares (USD$) junto con su llamada de función — ni enviar Bitcoin.

Pero en Ethereum, ya que tanto el dinero (Ether), los datos (payload de transacción) y el mismo código de contrato viven en Ethereum, es posible para ti llamar a una función y pagar dinero por el contrato al mismo tiempo.

Esto abarca una lógica realmente interesante, como requerir cierto pago por el contrato para, de esta manera, ejecutar una función.

## Veamos un ejemplo

contract OnlineStore {
  function buySomething() external payable {
    // Check to make sure 0.001 ether was sent to the function call:
    require(msg.value == 0.001 ether);
    // If so, some logic to transfer the digital item to the caller of the function:
    transferThing(msg.sender);
  }
}


Aquí, msg.value es una manera de ver cuanto Ether fue enviado al contrato, y ether es una unidad incorporada.

Lo que sucede aquí es que alguien llamaría a la función desde web3.js (desde la interfaz JavaScript del DApp) de esta manera:

// Assuming `OnlineStore` points to your contract on Ethereum:
OnlineStore.buySomething({from: web3.eth.defaultAccount, value: web3.utils.toWei(0.001)})
Nótese el campo value, donde la llamada de función javascript especifíca cuánto de ether enviar (0.001). Si piensas en la transacción como un sobre, y los parámetros que envías a la llamada de función son los contenidos de la carta que colocas adentro, entonces añadir un value es como poner dinero en efectivo dentro del sobre — la carta y el dinero son entregados juntos al receptor.

Nota: Si una función no es marcada como payable y usted intenta enviar Ether a esta, como se hizo anteriormente, la función rechazará su transacción.


Pongalo a prueba
Vamos a crear una función payable en nuestro juego zombie.

Digamos que nuestro juego tiene una función donde los usuarios pueden pagar ETH para subir el nivel de sus zombies. El ETH será almacenado en el contrato, el cual usted posee — ¡Esto es tan solo un ejemplo de cómo podría hacer dinero en sus juego

1. Defina un uint llamado levelUpFee y configurelo como igual a 0.001 ether.

2. Cree una función llamada levelUp. Esta tomará un parametro, _zombieId, un uint. Debería ser external y payable.

3. La función primero debería require (requerir) que msg.value sea igual a levelUpFee.

Luego el level de este zombie debería incrementar: zombies[_zombieId].level++.


pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  // 1. Define levelUpFee here
  uint levelUpFee = 0.001 ether;

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  // 2. Insert levelUp function here
  function levelUp(uint _zombieId) external payable {
    require (msg.value == levelUpFee);
    zombies[_zombieId].level++;
  }

  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) {
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }

  function getZombiesByOwner(address _owner) external view returns(uint[]) {
    uint[] memory result = new uint[](ownerZombieCount[_owner]);
    uint counter = 0;
    for (uint i = 0; i < zombies.length; i++) {
      if (zombieToOwner[i] == _owner) {
        result[counter] = i;
        counter++;
      }
    }
    return result;
  }

}
