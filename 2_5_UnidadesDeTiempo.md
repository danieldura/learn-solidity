Conceptos Avanzados de Solidity -> Capítulo 5: Unidades de Tiempo

creemos el sistema de batalla, los zombis que ganen más batallas subirán de nivel y tendrán acceso a más habilidades.

La propiedad readyTime requiere algo más de explicación. El objetivo es añadir un "periodo de enfriamento", una cantidad de tiempo que el zombi debe esperar después de atacar o alimentarse antes de poder volver a hacerlo. Sin esto, el zombi podría atacar y multiplicarse 1.000 veces al día, lo que haría muy fácil el juego.

Para controlar el tiempo que necesita esperar un zombi antes de volver a atacar, podemos usar las unidades de tiempo de Solidity.


Unidades de tiempo
Solidity proporciona algunas unidades nativas para trabajar con el tiempo.

La variable now devolverá el actual tiempo unix (la cantidad de segundos que han pasado desde el 1 de Enero de 1970). El tiempo unix cuando escribía esto es 1515527488.

Nota: El tiempo unix es tradicionalmente guardado en un número de 32 bits. Esto nos llevará a el problema del "Año 2038", donde las variables timestamp de tipo unix desbordarán y dejará inservibles muchos sistemas antiguos. Así que si queremos que nuestra DApp siga funcionando después de 20 años, podemos usar un número de 64 bits - pero de mientras nuestros usuarios tendrán que gastar más gas para usar nuestra DApp. ¡Decisiones de diseño!

Solidity también contiene segundos, minutos, horas, días, semanas y años como unidades de tiempo. Estos convertirán a un uint la cantidad de segundos que contengan esos números. Es decir 1 minuto son 60, 1 hora son 3600 (60 segundos x 60 minutos), 1 día son 86400 (24 horas x 60 minutos x 60 segundos), etc.

Aquí un ejemplo de como estas unidades pueden ser útiles:
uint lastUpdated;

// Ajustamos `lastUpdated` a `now`
function updateTimestamp() public {
  lastUpdated = now;
}

// Devolverá `true` si han pasado 5 minutos desde que `updateTimestamp`
// fue llamado, `false` si no han pasdo 5 minutos todavía
function fiveMinutesHavePassed() public view returns (bool) {
  return (now >= (lastUpdated + 5 minutes));
}
Puedes usar estas unidades de tiempo para la característica de enfriamiento de nuestro Zombi.


Vamos a probarlo
Vamos a añadir un tiempo de enfriamiento a nuestra DApp, y hacer que los zombis tengan que esperar 1 día antes de volver atacar o alimentarse.

Declara un uint llamado cooldownTime, inicializalo a 1 days. (Olvídate de la gramática pobre - si lo inicializas a "1 day", ¡No va a compilar!)

Como añadimos level y readyTime a nuestra estructura Zombie en el anterior capítulo, necesitamos actualizar _createZombie() para usar el número correcto de argumentos cuando creemos una nueva estructura Zombie.

Actualiza la línea zombies.push para añadir 2 argumentos más: 1 (para level), y uint32(now + cooldownTime) (para readyTime).

Nota: El uint32(...) es necesario porque now devuelve un uint256 por defecto. Así que necesitamos convertirlo explícitamente a uint32.

now + cooldownTime será igual al tiempo unix (en segundos) más el numero en segundos de 1 día — que será igual que el tiempo unix en segundos de 1 día a partir de hoy. Más adelante podemos comparar si el readyTime del zombi es mayor a now para ver si ha pasado el suficiente tiempo para poder volver a usarlo.

En el próximo capítulo implementaremos la funcionalidad para limitar las acciones basadas en readyTime.