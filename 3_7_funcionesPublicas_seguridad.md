Capítulo 7: Funciones Públicas & Seguridad


Ahora vamos a modificar feedAndMultiply para añadir el temporizador de enfriamiento en la cuenta.

Volviendo atrás en esta función, puedes ver que la hicimos public en la lección anterior. Una práctica importante de seguridad es examinar todas tus funciones public y external, y prueba a pensar las maneras en las que los usuarios podrían abusar de ellas. Recuerda - a no ser que estas funciones tengan un modificador como onlyOwner, cualquier usuario puede llamarlas y pasarles los datos que quieran.

Reexaminando esta particular función, el usuario podrá llamar a la función directamente y pasarle el _targetDna o _species que quiera. Esto no parece tener mucha jugabilidad - ¡queremos que sigan nuestras reglas!

Si nos damos cuenta, esta función solo necesita ser llamada por feedOnKitty(), así que la manera más fácil de prevenir estos problemas es hacer la función internal.

Vamos a probarlo
Actualmente feedAndMultiply es una función public. Vamos a hacerla internal para que el contrato sea más seguro. No queremos que los usuarios puedan llamar a esta función con el ADN que ellos quieran.

Vamos a hacer que feedAndMultiply utilice nuestro cooldownTime en la cuenta. Primero, despues de buscar myZombie, vamos a añadirle una sentencia require que compruebe _isReady() y le pase myZombie. De esta manera el usuario solo podrá ejecutar esta función si el enfriamiento del zombi ha terminado.

Al final de la función, vamos a llamar a _triggerCooldown(myZombie) para que al alimentarse ejecute el trigger del enfriamiento del zombi.