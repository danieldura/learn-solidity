SafeMath Part 2
https://cryptozombies.io/es/lesson/5/chapter/10

# Capítulo 10: SafeMath Part 2
Echemos un vistazo al código detrás de SafeMath:


```s
library SafeMath {

  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
    if (a == 0) {
      return 0;
    }
    uint256 c = a * b;
    assert(c / a == b);
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns (uint256) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint256 c = a / b;
    // assert(a == b * c + a % b); // There is no case in which this doesnt hold return c;
  }

  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }

  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}
```

Primero, tenemos la palabra clave reservada library— las librerías son similares a los contracts pero con algunas diferencias. Para nuestros propósitos, las librerías nos permiten usar la palabra clave reservada using, que automáticamente asocia a todos los métodos de la librería con unos tipos de datos:


```s
using SafeMath for uint;
// now we can use these methods on any uint
uint test = 2;
test = test.mul(3); // test now equals 6
test = test.add(5); // test now equals 11
```

Fíjese que las funciones mul y add requieren dos parámetros de entrada, pero cuando declaramos using SafeMath for uint, el uint al que llamamos en la función (test) se pasa automáticamente como parámetro.

Veamos el código que tiene add para ver qué hace SafeMath:


```s
function add(uint256 a, uint256 b) internal pure returns (uint256) {
  uint256 c = a + b;
  assert(c >= a);
  return c;
}
```

Básicamente add suma 2 valores uints como hace el símbolo +, pero también contiene una declaración assert para asegurarse de que la suma sea mayor que a. Esto nos protege de desbordamientos por exceso (overflows).

assert es similar a require, donde lanzará un error si es falso. La diferencia entre assert y require es que require devolverá al usuario el resto del gas cuando la función falle, mientras que assert no lo hará. Por lo tanto, la mayor parte del tiempo deseará utilizar require en su código; assert se usará normalmente sólo cuando algo ha ido terriblemente mal con el código (como un desbordamiento en uint).

Entonces, en pocas palabras, las funciones de SafeMath's de add, sub, mul, ydiv son funciones que realizan las 4 operaciones básicas de matemáticas, pero lanzan un error si ocurre un desbordamiento por exceso o por defecto.


# Usando SafeMath en nuestro código.
Para evitar el desbordamiento, podemos buscar en nuestro código los lugares donde se utilicen +, -, *, o /, y sustituirlos por add, sub, mul, div.

Ejemplo. En lugar de escribir:

`myUint++;`

Deberíamos poner:
`myUint = myUint.add(1);`


# Vamos a probarlo
Tenemos dos sitios en ZombieOwnership donde se hacen operaciones matemáticas. Vamos a sustituirlos por los métodos que proporciona SafeMath.

Reemplace ++ por su método correspondiente de SafeMath.

Reemplace -- por su método correspondiente de SafeMath.


```s

pragma solidity ^0.4.19;

import "./zombieattack.sol";
import "./erc721.sol";
import "./safemath.sol";

contract ZombieOwnership is ZombieAttack, ERC721 {

  using SafeMath for uint256;

  mapping (uint => address) zombieApprovals;

  function balanceOf(address _owner) public view returns (uint256 _balance) {
    return ownerZombieCount[_owner];
  }

  function ownerOf(uint256 _tokenId) public view returns (address _owner) {
    return zombieToOwner[_tokenId];
  }

  function _transfer(address _from, address _to, uint256 _tokenId) private {
    // 1. Replace with SafeMath's `add`
    ownerZombieCount[_to] = ownerZombieCount[_to].add(1);
    // 2. Replace with SafeMath's `sub`
    ownerZombieCount[_from] = ownerZombieCount[_from].sub(1);
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

```$$
