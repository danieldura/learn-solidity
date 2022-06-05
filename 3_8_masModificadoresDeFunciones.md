Capítulo 8: Más sobre Modificadores de Funciones

¡Genial! Nuestro zombi tiene ahora un temporizador de enfriamiento.

Ahora, vamos a añadir algunos métodos auxiliares. Hemos creado un nuevo fichero por tí llamado zombiehelper.sol, que importa zombiefeeding.sol. Esto nos ayudará a tener nuestro código organizado.

Vamos a hacer que nuestros zombis adquieran una habilidad especial después de alcanzar cierto nivel. Pero para hacer esto, primero necesitamos aprender un poco más sobre los modificadores de funciones.


Modificadores de funciones con argumentos
Antes hemos visto el ejemplo simple de onlyOwner. Pero un modificador de función puede también recibir argumentos. Por ejemplo:


// Un mapeo para guardar la edad de un usuario:
mapping (uint => uint) public age;

// Modificador que requiere que ese usuario sea mayor a cierta edad:
modifier olderThan(uint _age, uint _userId) {
  require (age[_userId] >= _age);
  _;
}

// Tiene que ser mayor a 16 años para conducir un coche (en EEUU, al menos).
// Podemos llamar al modificador de función `olderThan` pasandole argumentos de esta manera:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // La lógica de la función
}

Puedes ver aquí como el modificador olderThan recibe los argumentos de la misma manera que las funciones. Y cómo la función driveCar le pasa sus argumentos al modificador.

Vamos a probar a hacer nuestro propio modifier que utilice la propiedad level del zombi para restringir el acceso a las habilidades especiales.

Vamos a probarlo
En ZombieHelper, crea un modifier llamado aboveLevel. Este recibirá 2 argumentos, _level (un uint) y _zombieId (también un uint).

El cuerpo de la función deberá comprobar que zombies[_zombieId].level es mayor o igual a _level.

Recuerda que para que el modificador pueda ser llamado por el resto de funciones debe incluir _; en la última línea.


pragma solidity ^0.4.19;

import "./zombiefeeding.sol";

contract ZombieHelper is ZombieFeeding {

  // Empieza aquí
  modifier aboveLevel(uint _level, uint _zombieId) {
    require(zombies[_zombieId].level >= _level);
    _;
  }

}