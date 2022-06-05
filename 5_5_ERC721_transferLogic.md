# Capítulo 5: ERC721: Transfer Logic
¡Genial!. Hemos resuelto el conflicto.

Ahora vamos a continuar con nuestra implementación del ERC721 fijándonos en la función que transfiere la propiedad de una persona a otra.

Fíjese que la especificación de ERC721 tiene 2 formas distintas de poder transferir tokens:

`function transfer(address _to, uint256 _tokenId) public;`
`function approve(address _to, uint256 _tokenId) public;`
`function takeOwnership(uint256 _tokenId) public;`

1. La primera forma consiste en que el propietario llame a **transfer** con la dirección **address** a la cual quiere enviar, y el **_tokenId** del token que quiere transferir.

2. La segunda forma consiste en que el propietario llame primero a **approve**, y envía la misma información que el caso anterior. Después, el contrato almacena quién está autorizado para tomar un token, generalmente en un **mapping (uint256 => address)**. Entonces, cuando alguien llame a **takeOwnership**, el contrato comprueba si ese **msg.sender** está autorizado por el propietario para tomar ese token y si es así, le transfiere el token.

Si te das cuenta, tanto **transfer** como **takeOwnership** contienen la misma lógica para la transferencia, sólo que en orden inverso. (En un caso, el emisor del token llama a la función, en el otro el receptor del token lo llama).

Entonces, tiene sentido que abstraigamos esta lógica en su propia función privada, **_transfer**, que luego es llamada por ambas funciones. De esa forma no repetiremos el mismo código dos veces.


# Vamos a probarlo
Vamos a definir la lógica de _transfer.

1. Define una función llamada _transfer. Recibirá 3 parámetros, address _from, address _to, y uint256 _tokenId. Deberá ser una función privada con private.

2. Tenemos 2 mappings que cambiarán cuando el propietario cambie: ownerZombieCount (que hace un seguimiento de cuántos zombis tiene cada propietario) y zombieToOwner (que realiza un seguimiento de quién posee cada zombi en concreto).

Lo primero que hará nuestra función será incrementar el valor de ownerZombieCount para la persona que recibirá el zombie (address _to). Use ++ para incrementarlo.

3. Después, tendrémos que disminuir el valor de ownerZombieCount de la persona que envía el zombie desde (address _from). Use -- para disminuir.

4. Finalmente, tendremos que cambiar el mapping de zombieToOwner para el ID _tokenId , que ahora deberá apuntar a _to.

5. Te mentí, ese no era el último paso. Hay una cosa más que debemos hacer.

La especificación ERC721 incluye un evento **Transfer**. La última línea de esta función debe activar **Transfer** con la información correcta — revise **erc721.sol** para ver con qué parámetros se espera que se llame e impleméntalo aquí.
`event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);`

```
  // Define _transfer() here
  function _transfer(address _from, address _to, uint256 _tokenId) private {
    ownerZombieCount[_to]++;
    ownerZombieCount[_from]--;
    zombieToOwner[_tokenId] = _to;
    Transfer(_from, _to, _tokenId);
  }

  ```