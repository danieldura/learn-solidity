# Capítulo 8: ERC721: takeOwnership

Genial. ¡Ahora terminemos nuestra implementación de ERC721 con la última función! (No te preocupes, todavía hay más por cubrir en la Lección 5 después de esto 😉)

La última función, **takeOwnership**, simplemente debe verificar que **msg.sender** ha sido aprobado para tomar este token / zombie, y llamar a **_transfer** si es así.

# Vamos a probarlo

1. Primero, queremos usar la declaración require para verificar que zombieApprovals para _tokenId es igual a msg.sender.

De esta forma, si msg.sender no ha sido aprobado para tomar este token, lanzará un error.

2. Para llamar a _transfer, necesitamos saber la dirección de la persona que posee el token (requiere _from como parámetro). Afortunadamente, podemos buscar esto con nuestra función ownerOf.

Así que, declare una variable tipo address llamada owner, ponga como valor el mismo que devuelve ownerOf(_tokenId).

3. Finalmente, llame a _transfer, y pase toda la información necesaria. (Aquí puede usar msg.sender para el valor _to, ya que la persona que llama a esta función es a la que debe enviarse el token).

_Nota: Podríamos haber hecho los pasos 2 y 3 en una línea de código, pero dividirla hace que las cosas sean un poco más legibles. Preferencia personal._

```s
pragma solidity ^0.4.19;

import "./zombieattack.sol";
import "./erc721.sol";

contract ZombieOwnership is ZombieAttack, ERC721 {

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

  function approve(address _to, uint256 _tokenId) public onlyOwnerOf(_tokenId) {
    zombieApprovals[_tokenId] = _to;
    Approval(msg.sender, _to, _tokenId);
  }

  function takeOwnership(uint256 _tokenId) public {
    require(zombieApprovals[_tokenId] == msg.sender);
    address owner = ownerOf(_tokenId);
    _transfer(owner, msg.sender, _tokenId);

  }
}
```