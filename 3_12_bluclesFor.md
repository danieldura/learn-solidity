Capítulo 12: Bucles For
En el capítulo anterior, mencionamos que a veces queremos usar bucles for para construir contenido dentro de un array en una función antes que simplemente guardar el array en storage.

Vamos a ver el por qué.

Para nuestra función getZombiesByOwner, una implementación nativa sería guardar un mapping de dueños para los ejércitos zombis en el contrato ZombieFactory:


mapping (address => uint[]) public ownerToZombies
De esta forma cada vez que creemos un nuevo zombi, simplemente tenemos que usar ownerToZombies[owner].push(zombieId) para añadirlo al ejército de zombis de ese usuario. Y getZombiesByOwner sería una función tan sencilla como:

function getZombiesByOwner(address _owner) external view returns (uint[]) {
  return ownerToZombies[_owner];
}


El problema con este enfoque:
Este planteamiento es tentador por su simplicidad. Pero vamos a ver que pasa si más tarde añadimos una función para transferir un zombi de un dueño a otro (¡cosa que querremos hacer en una lección próxima!).

Esa función de transferencia necesitaría que:

1. Añadir el zombi al array ownerToZombies del nuevo dueño,
2. Eliminar el zombi del array ownerToZombies del antiguo dueño,
3. Mover todos los zombis en el array del antiguo dueño para rellenar el hueco que hemos dejado, y
4. Reducir el largo del array en 1.

El paso 3 costará demasiado gas, debido a que tenemos que hacer una escritura por cada zombi que queramos mover. Si un usuario tiene 20 zombis y cambia el primero de todos, tenemos que hacer 19 escrituras para mantener el orden del array.

Como escribir en storage es una de las operaciones más caras en Solidity, cada llamada a la función de transferencia será exteremadamente cara en cuanto al gas. Y lo peor, la función costará diferente cantidad de gas cada vez que se llame, dependiendo de cuantos zombis tenga el usuario en su ejército y la cantidad de zombis a mover. Así que el usuario no sabrá cuanto gas enviar.

Nota: Por supuesto, podemos mover el último zombi del array para rellenar el hueco que hemos dejado y reducir la longitud del array en uno. Pero cambiaremos el orden de nuestro ejército cada vez que hagamos una transferencia de zombi.

Debido a que las funciones view no consumen gas cuando son llamadas externamente, podemos simplemente usar un bucle loop en getZombiesByOwner para iterar en el array de zombis y construir un array de zombis que pertenezcan a un usuario específico. Con esto nuestra función de transferencia será mucho más barata, ya que no necesitamos reordenar ningún array en storage, y porque este enfoque ligeramente contraintuitivo es integramente más barato.

# Usando los bucles for
La sintaxis de los bucles for en Solidity es similar a JavaScript.

Vamos a ver un ejemplo donde queremos hacer un array de números pares:

function getEvens() pure external returns(uint[]) {
  uint[] memory evens = new uint[](5);
  // Guardamos el índice del nuevo array:
  uint counter = 0;
  // Iteramos del 1 al 10 con un bucle for:
  for (uint i = 1; i <= 10; i++) {
    // Si `i` es par...
    if (i % 2 == 0) {
      // Añadelo a nuestro array
      evens[counter] = i;
      // Incrementamos el contador al nuevo índice vacío de `evens`:
      counter++;
    }
  }
  return evens;
}
La función devolverá un array con este contenido [2, 4, 6, 8, 10].

# Vamos a probarlo

Vamos a terminar nuestra función getZombiesByOwner escribiendo un bucle for que itere todos los zombis de nuestra DApp, comparando su dueño para que cuando coincida con el buscamos, lo añada al array result antes de devolverlo.

Declara un uint llamado counter e inicializalo a 0. Usaremos esta variable para mantener el control del índice en nuestro array result.

Declara un bucle for que empiece en uint i = 0 y vaya hasta i < zombies.length. Este iterará todos los zombis de nuestro array.

Dentro del bucle for, crea una sentencia if que compruebe si zombieToOwner[i] es igual a _owner. Esto comparará dos direcciones para ver si son iguales.

Dentro de la sentencia if:

Añadimos la ID del zombi a nuestro array result haciendo que result[counter] sea igual a i.
Incrementamos counter en 1 (mira el bucle for del ejemplo de arriba).
Eso es - la función ahora devolverá todos los zombis del usuario _owner sin gastar nada de gas.

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