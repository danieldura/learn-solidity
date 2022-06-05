# Capítulo 7: Más Refactorización
Tenemos un par de lugares más en zombiehelper.sol donde necesitamos implementar nuestro nuevo modifier ownerOf.

# Vamos a probarlo
Actualice changeName() para utilizar ownerOf

Actualice changeDna() para utilizar ownerOf

````
  function changeName(uint _zombieId, string _newName) external aboveLevel(2, _zombieId) ownerOf(_zombieId) {
    zombies[_zombieId].name = _newName;
  }

  function changeDna(uint _zombieId, uint _newDna) external aboveLevel(20, _zombieId) ownerOf(_zombieId) {
    zombies[_zombieId].dna = _newDna;
  }
```