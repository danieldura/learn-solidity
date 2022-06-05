# Capítulo 7: ERC721: Approve
Ahora, vamos a implementar **approve.**

Recuerda, con **approve / takeOwnership**, la transferencia se produce en 2 pasos:

1. Tú, el propietario, llamas a approve y envías la dirección address del nuevo propietario, y el _tokenId que quieres enviar.

2. El nuevo propietario llama a **takeOwnership** con el **_tokenId**, el contrato comprobará que este nuevo usuario está autorizado para ello y si es correcto, le transferirá el token.

Como esto ocurre durante 2 llamadas distintas, necesitamos una estructura de datos para almacenar quién ha sido aprobado para cada cosa, en cada uno de los pasos donde se produce cada llamada a una función


# Vamos a probarlo

Primero, definamos el mapping zombieApprovals. Deberá ser un map de tipo uint con una dirección address.

De esta forma, cuando alguien llame a takeOwnership con un _tokenId, podremos usar este mapping para buscar rápidamente quién está aprobado para tomar ese token.

En la función approve , queremos asegurarnos de que solo el propietario del token pueda dar la aprobación para que otra persona pueda tomar el token. Así que necesitaremos el modifier de onlyOwnerOf para usar approve.

En el cuerpo de la función, establezca zombieApprovals para _tokenId con la dirección de _to.

Finalmente, hay un evento Approval en la especificación ERC721. Entonces, debemos activar este evento al final de esta función. Revise los parámetros en erc721.sol , y asegúrese de utilizar msg.sender para el campo _owner.


```s
pragma solidity ^0.4.19;

import "./zombieattack.sol";
import "./erc721.sol";

contract ZombieOwnership is ZombieAttack, ERC721 {

  // 1. Define mapping here
  mapping (uint => address) zombieApprovals;

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

  function transfer(address _to, uint256 _tokenId) public onlyOwnerOf(_tokenId) {
    _transfer(msg.sender, _to, _tokenId);
  }

  // 2. Add function modifier here
  function approve(address _to, uint256 _tokenId) public onlyOwnerOf(_tokenId) {
    zombieApprovals[_tokenId] = _to;
    Approval(msg.sender, _to, _tokenId);
  }

  function takeOwnership(uint256 _tokenId) public {

  }
}
```