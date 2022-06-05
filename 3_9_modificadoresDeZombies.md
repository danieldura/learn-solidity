# Capítulo 9: Modificadores de Zombis
Ahora vamos a usar nuestro modificador aboveLevelpara crear algunas funciones.

Nuestro juego tendrá algunos incentivos para la gente que suba de nivel a sus zombis:

- Para los zombis de nivel 2 y superior, los usuarios podrán cambiarle el nombre.
- Para los zombis de nivel 20 y superior, los usarios podrán darle un ADN personalizado.

Vamos a implementar estas funciones abajo. Aquí tienes un ejemplo de la lección anterior como referencia:

// Un mapeo para guardar la edad del usuario:
mapping (uint => uint) public age;

// Modificador que requiere que ese usuario sea mayor a cierta edad:
modifier olderThan(uint _age, uint _userId) {
  require (age[_userId] >= _age);
  _;
}

// Tiene que ser mayor a 16 años para conducir un coche (en EEUU, al menos).
function driveCar(uint _userId) public olderThan(16, _userId) {
  // La lógica de la función
}

Vamos a probarlo
1. Crea una función llamada changeName. Esta recibirá 2 argumentos: _zombieId (un uint), y _newName (un string), y hazla external. Esta deberá tener el modificador aboveLevel, y le pasará 2 como parámetro _level. (No te olvides de pasarle el _zombieId también).

2. En esta función, primero tenemos que verificar que el msg.sender es igual a zombieToOwner[_zombieId]. Utiliza una sentencia require.

3. Después la función deberá fijar zombies[_zombieId].name como _newName.

4. Crea otra función llamada changeDna debajo de changeName. Su definición y contenido tendrá que ser igual que el de changeName, a excepción de su segundo argumento que será _newDna (un uint), y debemos pasarle 20 para el parámetro _level en el modificador aboveLevel. Y por supuesto, este deberá fijar el dna del zombi a _newDna en vez de cambiarle el nombre.

pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

  // Empieza aquí
  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId){
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId){
    require(msg.sender == zombieToOwner[_zombieId]);
    zombies[_zombieId].dna = _newDna;
  }

}
