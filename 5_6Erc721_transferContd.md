# Capítulo 6: ERC721: Transfer Cont'd

¡Genial!. Esa fue la parte difícil — ahora implementar la función transfer pública será fácil, ya que nuestra función **_transfer** hace ya casi todo el trabajo.

# Vamos a probarlo
1. Queremos asegurarnos de que solo el dueño de un token / zombie pueda transferirlo. ¿Recuerda cómo podemos restringir el acceso a una función solo a su propietario?

Correcto, ya tenemos un modifier que hace esto. Así que añade el modifier llamado onlyOwnerOf a esta función.

2. Ahora el cuerpo de la función realmente solo necesita tener una línea. Sólo necesita llamar a _transfer.

Asegúrate de pasar msg.sender en la dirección address _from del parámetro.

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

  function _transfer(address _from, address _to, uint256 _tokenId) private {
    ownerZombieCount[_to]++;
    ownerZombieCount[_from]--;
    zombieToOwner[_tokenId] = _to;
    Transfer(_from, _to, _tokenId);
  }

  // 1. Add modifier here
  function transfer(address _to, uint256 _tokenId) public onlyOwnerOf(_tokenId) {
    _transfer(msg.sender, _to, _tokenId);
  }

  function approve(address _to, uint256 _tokenId) public {

  }

  function takeOwnership(uint256 _tokenId) public {

  }
}
```