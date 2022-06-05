Capítulo 6: Enfriamiento de los Zombis

Ahora que tenemos la propiedad readyTime en nuestra estructura Zombie, vamos a pasar a zombiefeeding.sol e implementar el contador de enfriamiento.

Vamos a modificar nuestro feedAndMultiply de tal manera que:

Alimentarse activa el enfriamiento del zombi, y

Los zombis no podrán alimentarse de gatitos hasta que su periodo de enfriamiento haya concluido

Esto hará que los zombis no se alimenten de gatitos ilimitados y se multipliquen durante todo el día. En el futuro, cuando añadamos la funcionalidad de batalla, haremos que el atacar a otros zombis también tenga su enfriamiento.

Primero, vamos a definir alguna función auxiliar que nos ajuste y verifique el readyTime del zombi.


Pasando estructuras como argumentos
Puedes pasar un puntero storage a una estructura como argumento a una función private o internal. Esto es práctico, por ejemplo, para pasar entre funciones la estructura de nuestro Zombie.

La sintaxis sería algo así:

function _doStuff(Zombie storage _zombie) internal {
  // hacer cosas con _zombie
}
De esta manera podemos pasar una referencia a nuestro zombi en una función en vez de pasar la ID del zombi y comprobar cual es.

Vamos a probarlo
Empieza definiendo una función _triggerCooldown. Esta tomará 1 argumento, _zombie, un puntero a Zombie storage. La función deberá ser internal.

El cuerpo de la función deberá inicializar _zombie.readyTime a uint32(now + cooldownTime).

Luego, crea una función llamada _isReady. Esta función tambien recibirá un argumento Zombie storage llamado _zombie. Esta será una función internal view, y devolverá un bool.

El cuerpo de la función deberá devolver (_zombie.readyTime <= now), que evaluará si es true o false. Esta función nos dirá si ha pasado el suficiente tiempo desde la última vez que un zombi se alimentó.