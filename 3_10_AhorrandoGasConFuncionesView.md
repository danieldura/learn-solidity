Ahorrando Gas Con Funciones 'View'

¡Increible! Ahora tenemos algunas habilidades especiales para los zombis de nivel alto, dandoles a sus dueños un incentivo por subirlos de nivel. Si queremos podemos añadir más de estos en el futuro.

Vamos a añadir una nueva función: nuestra DApp necesita un método para ver el ejército de un usuario - vamos a llamarlo getZombiesByOwner.

Esta función solo necesita leer datos de la blockchain, por lo que podemos hacer una función view. Esta nos brinda un importante tema a la hora de hablar sobre la optimización de gas:


# Las funciones view no cuestan gas cuando son llamadas externamente por un usuario.

no cambia nada en la blockchain - solo leen datos. Así que marcar una función con view le dice a *web3.js* que solo necesita consultar a tu nodo local de Ethereum para ejecutar la función, y que no necesita crear ninguna transacción en la blockchain (la cual debería ejecutarse por todos y cada uno de los nodos, y costaría gas).

Hablaremos sobre configurar web3.js en tu propio nodo mas tarde. Por ahora la mayor ventaja es que puedes optimizar el uso de gas de tu DApp haciendo que tus usuarios utilicen funciones external view siempre que sea posible.


Nota: Si una función view es llamada internamente por otra función que no sea view en el mismo contrato, esta seguira costando gas. Esto es porque la otra función crea una transacción en Ethereum, y necesita ser verificada por el resto de nodos. Por lo que las funciones view son gratuitas siempre que sean llamadas externamente.


Vamos a probarlo
Vamos a implementar una función que devuelva el ejército de un usuario. Más adelante podemos llamar a esta función desde web3.js si queremos mostrar la página de perfil de un usuario con todo su ejército.

La lógica de esta función es algo complicada por lo que nos llevará algunos capítulos implementarla.

1. Crea una nueva función llamda getZombiesByOwner. Esta recibirá un argumento, un address llamado _owner.

2. Vamos a hacer la función external view, para poder llamarla desde web3.js sin necesidad de usar gas.

3. La función deberá devolver un uint[] (un array de uint).

Deja el cuerpo de la función vacío por ahora, lo rellenaremos en el siguiente capítulo.

  function getZombiesByOwner(address _owner) external view returns (uint[]){

  }