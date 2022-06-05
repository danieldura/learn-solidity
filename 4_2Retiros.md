# Capítulo 2: Retiros
En el capitulo anterior, aprendimos cómo enviar Ether a un contrato. Entonces ¿Qué ocurre cuando lo envía?

Luego de enviar Ether a un contrato, este se almacena en la cuenta de Ethereum del contrato y estará atrapado ahí — a menos que añada una función para retirar el Ether del contrato

Puede escribir una función para retirar Ether del contrato de la siguiente forma:

contract GetPaid is Ownable {
  function withdraw() external onlyOwner {
    owner.transfer(this.balance);
  }
}

Nótese que estamos utilizando owner y onlyOwner del contrato Ownable, asumiendo que este fue importado.

Puede transferir Ether a una dirección utilizando la función transfer y this.balance devolverá el balance total almacenado en el contrato. Así que si 100 usuarios han pagado 1 Ether a nuestro contrato, this.balance sería igual a 100 Ether.

Puede utilizar transfer para enviar fondos a cualquier dirección de Ethereum. Por ejemplo, podría tener una función que transfiera Ether de vuelta al msg.sender si rebasan el precio al pagar un item.

uint itemFee = 0.001 ether;
msg.sender.transfer(msg.value - itemFee);
O en un contrato con un comprador y un vendedor, usted podría guardar la dirección del vendedor en la memoria, luego, cuando alguien adquiera su item, transferirle la tasa pagada por el comprador: seller.transfer(msg.value).

Estos son algunos ejemplos de lo que hace de la programación de Ethereum algo realmente genial — puede tener mercados descentralizados como este que no son controlados por nadie.

# Pongalo a prueba
1. Cree una función withdraw en nuestro contrato, la cual debería ser idéntica al ejemplo GetPaid anterior.

2. El precio del Ether aumentó x10 en el año pasado. Así que mientras 0.001 ether es como $1 en el momento de escribir esto, si sube x10 de nuevo, 0.001 ETH sería equivalente a $10 y nuestro juego será mucho más costoso.

Así que es una buena idea crear una función que nos permita, como dueños del contrato, configurar el levelUpFee.

a. Cree una función llamada setLevelUpFee que tome un argumento, uint _fee, sea external y utilice el modificador onlyOwner.

b. La función debería configurar levelUpFee como igual a _fee.

pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  uint levelUpFee = 0.001 ether;

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  // 1. Crear la función withdraw aquí
  function withdraw () external onlyOwner {
    owner.transfer(this.balance);
  }

  // 2. Crear la función setLevelUpFee aquí
  function setLevelUpFee(uint _fee) external onlyOwner {
    levelUpFee = _fee;
  }

  function levelUp(uint _zombieId) external payable {
    require(msg.value == levelUpFee);
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
