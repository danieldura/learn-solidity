Leccion 5: ERC721 & Crypto-Coleccionables

¡Wow! Se está animando la cosa por aquí...

En esta lección, vamos a ser un poco más avanzados.

Ahora vamos a hablar sobre los tokens, el estándar ERC721 y los activos/bienes/objetos crypto-coleccionables.

En otras palabras, vamos a permitir que puedas intercambiar tus zombies con tus amigos.

# Hablemos de tokens.

Si has estado en el ecosistema de Ethereum durante un tiempo, es probable que hayas oído hablar de los tokens — en concreto, de los tokens ERC20.

Un token es básicamente un contrato inteligente que sigue algunas reglas comunes, es decir, implementa un conjunto estándar de funciones que comparten el resto de tokens (contratos), como por ejemplo transfer(address _to, uint256 _value) y balanceOf(address _owner).

Internamente, el contrato inteligente tiene por lo general un mapeo, mapping(address => uint256) balances, que realiza un seguimiento de cuánto saldo tiene cada dirección.

Así que, básicamente, un token es sólo un contrato que realiza un seguimiento de quién posee la cantidad de ese token y algunas funciones para que los usuarios puedan transferir sus tokens a otras direcciones.

# ¿Por qué importa?
Dado que todos los tokens ERC20 comparten el mismo conjunto de funciones con los mismos nombres, todos pueden interactuar de la misma manera.

Esto significa que si creas una aplicación que es capaz de interactuar con un token de tipo ERC20, también será capaz de interactuar con cualquier token de tipo ERC20. De esta forma, más tokens se pueden añadir fácilmente a su aplicación en el futuro, sin necesidad de tener códigos personalizados por cada uno de los tipos de token ERC20. Simplemente puede conectar la nueva dirección de contrato del token y boom, su aplicación tiene otro token que puede usar.

Un ejemplo de esto sería un exchange (casa de cambio). Cuando un exchange añade un nuevo token ERC20, realmente solo necesita agregar otro contrato inteligente con el que comunicarse. Los usuarios pueden indicarle a ese contrato que envíe los tokens a la dirección de su monedero en el exchange, y el exchange puede indicarle al contrato que devuelva los tokens a los usuarios cuando soliciten un retiro.

El exchange solo necesita implementar esta lógica de transferencia una vez, luego, cuando quiera añadir un nuevo token ERC20, es simplemente una cuestión de añadir la nueva dirección del contrato a su base de datos.


# Otros estándares de token
Los tokens ERC20 son realmente geniales porque actúan como si fueran monedas. Pero no son particularmente útiles para representar zombies en nuestro juego zombie.

Por un lado, los zombies no son divisibles como las monedas - puedo enviarte 0.237 ETH, pero transferir 0.237 de un zombie en realidad no tiene sentido.

En segundo lugar, todos los zombies no son creados iguales. Tu zombi de nivel 2 "Steve" no es igual a mi zombie de nivel 732 "H4XF13LD MORRIS 💯💯😎💯💯". (Ni estás cerca de serlo, Steve).

Hay otro estándar de token que encaja mucho mejor con los cripto-coleccionables como CryptoZombies, y se llaman tokens ERC721.

Los tokens ERC721 no son intercambiables entre sí, ya que se supone que cada uno de ellos es totalmente único e indivisible. Solo se pueden intercambiar en unidades completas, y cada uno tiene una ID única. Así que estos, son perfectos para hacer que nuestros zombies sean comerciables.

_Nota: Tenga en cuenta que el uso de un estándar como ERC721 tiene el beneficio de que no tenemos que implementar la lógica que está detrás del sistema que permite las operaciones de compra/venta en nuestro contrato, y que determina cómo los jugadores pueden comprar / vender nuestros zombies. Si cumplimos con la especificación, alguien más podría construir otra plataforma de intercambio para los activos ERC721, y nuestros zombies ERC721 se podrían usar en esa plataforma. Por lo tanto, existen beneficios claros en el uso de un token estándar en lugar de desarrollar su propia lógica comercial._


# Vamos a probarlo
Vamos a profundizar en la implementación de ERC721 en el próximo capítulo. Pero primero, configuremos nuestra estructura de archivos para esta lección.

Vamos a almacenar toda la lógica ERC721 en un contrato llamado ZombieOwnership.

Declare nuestra versión de pragma en la parte superior del archivo (verifique la sintaxis de los archivos de las lecciones anteriores).

Este fichero debe hacer un import de zombieattack.sol.

Declare un nuevo contrato ZombieOwnership, que herede de ZombieAttack. Deje el cuerpo del contrato vacío por el momento.

````
pragma solidity ^0.4.19;

import "./zombieattack.sol";

contract ZombieOwnership is ZombieAttack {

}

```
