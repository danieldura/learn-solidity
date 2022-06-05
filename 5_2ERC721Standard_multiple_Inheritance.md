Vamos a echar un vistazo al estándar ERC721:

```s
contract ERC721 {
  event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
  event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId);

  function balanceOf(address _owner) public view returns (uint256 _balance);
  function ownerOf(uint256 _tokenId) public view returns (address _owner);
  function transfer(address _to, uint256 _tokenId) public;
  function approve(address _to, uint256 _tokenId) public;
  function takeOwnership(uint256 _tokenId) public;
}
```
Esta es la lista de métodos que necesitaremos implementar, que haremos en los siguientes capítulos por partes.

Parece mucho, ¡pero no te sientas abrumado! Estamos aquí para guiarte a través de esto.

> Nota: El estándar ERC721 actualmente es un borrador, y aún no hay una implementación oficialmente acordada. Para este tutorial estamos utilizando la versión actual de la librería de OpenZeppelin, pero es posible que cambie en el futuro antes de su lanzamiento oficial. Así que considere esta como una posible implementación, pero no la tomes como el estándar oficial para los tokens ERC721.

# Implementando un contrato de token
Al implementar un contrato de token, lo primero que haremos es copiar el fichero de la interfaz a nuestro propio directorio e importarlo con import "./erc721.sol";. Luego, declararemos a nuestro contrato que herede de él y reescribiremos cada método con una definición de la función.

Pero espera — ZombieOwnership está ya heredando de ZombieAttack — ¿cómo heredaremos también de ERC721?

Afortunadamente en Solidity, nuestro contrato puede tener herencia múltiple de la siguiente forma:

```s
contract SatoshiNakamoto is NickSzabo, HalFinney {
  // Omg, the secrets of the universe revealed!
}
```
Como puede ver, para usar herencia múltiple sólo es necesario separar por comas , cada uno de los contratos múltiples. En este caso, nuestro contrato heredará de **NickSzabo** y **HalFinney**.

Vamos a intentarlo.

# Vamos a probarlo
Ya hemos creado erc721.sol con la interfaz por ti.

Importa erc721.sol en zombieownership.sol

Declara que ZombieOwnership hereda de ZombieAttack Y ERC721

```s
pragma solidity ^0.4.19;

import "./zombieattack.sol";
import "./erc721.sol";

// Declare ERC721 inheritance here
contract ZombieOwnership is ZombieAttack, ERC721 {

}
```