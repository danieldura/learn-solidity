# Capítulo 3: balanceOf & ownerOf
Genial. ¡Profundicemos en la implementación de ERC721!

Hemos avanzado por ti, copiando todas las funciones (en blanco) que se implementarán en esta lección.

En este capítulo, vamos a implementar los primeros dos métodos: **balanceOf** y **ownerOf**.

## balanceOf

`function balanceOf(address _owner) public view returns (uint256 _balance);`

Esta función simplemente recibe una dirección address, y devuelve cuántos tokens tiene esa dirección address.

En nuestro caso, nuestros "tokens" son Zombies. ¿Recuerdas en nuestra DApp dónde almacenamos cuántos zombis tiene cada propietario?

## ownerOf

  `function ownerOf(uint256 _tokenId) public view returns (address _owner);`

  Esta función recibe un ID de un token (en nuestro caso, un ID de un Zombie) y devuelve la dirección de la persona que lo posee.

De nuevo, esto es muy sencillo de implementar, ya que tenemos un mapeo mapping en nuestra DApp que ya almacena esa información. Podemos implementar esta función con sólo una línea, poniendo un simple return .

> Nota: Recuerda, **uint256** es equivalente a **uint**. Hemos estado usando uint en nuestro código hasta ahora, pero estamos usando uint256 aquí porque copiamos / pegamos desde la especificación de la librería importada, que requiere usar este tipo.

# Vamos a probarlo
Dejaré que te des cuenta de cómo implementar estas 2 funciones.

Cada función debería ser simplemente 1 línea de código, un return. Eche un vistazo a nuestro código de lecciones anteriores para ver dónde almacenamos estos datos. Si no puede resolverlo, puede presionar el botón "show me the answer / muéstrame la respuesta" para obtener ayuda.

Implemente balanceOf para que devuelva el número de zombies que _owner tiene.

Implemente ownerOf para que devuelva la dirección del dueño del zombi con ID _tokenId.

```s
pragma solidity ^0.4.19;

import "./zombieattack.sol";
import "./erc721.sol";

contract ZombieOwnership is ZombieAttack, ERC721 {

  function balanceOf(address _owner) public view returns (uint256 _balance) {
    return ownerZombieCount[_owner];
  }

  function ownerOf(uint256 _tokenId) public view returns (address _owner) {
    return zombieToOwner[_tokenId];
  }

  function transfer(address _to, uint256 _tokenId) public {

  }

  function approve(address _to, uint256 _tokenId) public {

  }

  function takeOwnership(uint256 _tokenId) public {

  }
}

```