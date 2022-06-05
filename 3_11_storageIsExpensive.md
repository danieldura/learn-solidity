Capítulo 11: Storage is Expensive
Una de las operaciones más caras en Solidity es usar storage — especialmente la escritura.

Esto es debido a que cada vez que escribes o cambias algún dato, este se *guarda permanentemente en la blockchain*. ¡Para siempre! Miles de nodos alrededor del mundo necesitan guardar esos datos en sus discos duros, y esa cantidad de datos sigue creciendo a lo largo del tiempo a medida que crece la blockchain. Así que tiene que haber un coste para hacer eso.

Para seguir manteniendo los costes bajos, querrás evitar escribir datos en "storage" a no ser que sea *absolutemente necesario*. A veces *esto implica* usar en *programación* una lógica *ineficiente* - como volver a construir un array en memoria cada vez que una función es llamada en vez de simplemente guardar ese array en una variable para acceder a sus datos más rápido.

En la mayoría de lenguajes de programación, usar bucles sobre largos conjuntos de datos es costoso. Pero en Solidity, esta es una manera más barata que usar storage si está en una función external view, debido a que las funciones view no les cuesta a los usuarios nada de gas. (¡Y el gas le cuesta a tus usuarios dinero real!).

Veremos los bucles for en el siguiente capítulo, pero primero, vamos a ver como declarar los arrays en memoria.


## Declarando arrays en memoria
Puedes usar la palabra clave memory con arrays para crear un nuevo arrays dentro de una función sin necesidad de escribir nada en storage. El array solo existirá hasta el final de la llamada de la función, y esto es más barato en cuanto a gas que actualizar un array en storage - gratis si está dentro de una función view llamada externamente.

Así es como se declara un array en memoria:

function getArray() external pure returns(uint[]) {
  // Instanciamos un nuevo array en memoria con una longitud de 3
  uint[] memory values = new uint[](3);
  // Le añadimos algunos valores
  values.push(1);
  values.push(2);
  values.push(3);
  // Devolvemos el arrays
  return values;
}

Esto es un ejemplo trivial para enseñarte a cómo usar la sintaxis, pero en el próximo capítulo veremos como combinarlo con bucles for para usarlo en casos de uso reales.

Nota: los arrays de tipo memory deben ser creados con una longitud como argumento (en este ejemplo, 3). Actualmente no pueden ser redimensionados como los arrays storage pueden serlo usando array.push(), de todas maneras esto podría cambiar en futuras versiones de Solidity.


Vamos a probarlo
En nuestra función getZombiesByOwner, queremos devolver un array uint[] con todos los zombis de un usuario particular.

1. Declara una variable uint[] memory llamada result

2. Fíjala a un nuevo array de tipo uint. El largo del array debería ser igual al número de zombis que posea su _owner, que puede saberse mirándolo en nuestro mapping con: ownerZombieCount[_owner].

3. Al final de la función devuelve result. Esta devolverá un array vacío por ahora, pero lo rellenaremos en el próximo capítulo.


  function getZombiesByOwner(address _owner) external view returns(uint[]) {
    // Empieza aquí
    uint[] memory result = new uint[](ownerZombieCount[_owner]);

    return result;
  }