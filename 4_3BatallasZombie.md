# Capítulo 3: Batallas Zombie
Ahora que hemos aprendido sobre funciones payable y balances de contrato ¡Es hora de añadir funcionalidad para las batallas zombie!

Siguiendo el formato de capitulos pasados, organizaremos nuestro código creando un nuevo archivo / contrato para la funcionalidad de ataque que se importa desde nuestro contrato anterior.

# Póngalo a prueba
Hagamos un repaso de cómo crear un nuevo contrato ¡La repetición hace al maestro!

Si no puede recordar la sintaxis para hacerla, vea zombiehelper.sol para ponerse al día — pero primero trate de hacerlo sin fijarse para poner a prueba su conocimiento.

Anuncie al principio del archivo que estamos utilizando Solidity en su versión ^0.4.19.

import (importe) desde zombiehelper.sol.

Anuncie un nuevo contract llamado ZombieBattle que herede de ZombieHelper. Deje vacío el cuerpo del contrato por ahora.

pragma solidity ^0.4.19;

import "./zombiehelper.sol";

contract ZombieBattle is ZombieHelper {
  
}