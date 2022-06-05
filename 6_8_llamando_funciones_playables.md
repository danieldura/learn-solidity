# Capítulo 8: Llamando funciones Payables
La lógica para attack, changeName, y changeDna será extremadamente similar, por lo que son triviales de implementar y no vamos a perder tiempo codificándolas en esta lección.

De hecho, ya hay mucha lógica repetitiva en cada una de estas funciones, por lo que probablemente tendría sentido refactorizar y poner el código común en su propia función. (Y usar un sistema de plantillas para los mensajes txStatus — ¡ya estamos viendo cuánto más limpias serían las cosas con un framework como Vue.js!) Veamos otro tipo de función que requiere un tratamiento especial en Web3.js — funciones payable.


# Subir de nivel
Recuerda que en ZombieHelper, añadimos una función payable donde el usuario podía subir de nivel a su zombi:

```s
function levelUp(uint _zombieId) external payable {
  require(msg.value == levelUpFee);
  zombies[_zombieId].level++;
}
```

La forma de enviar Ether junto con una función es simple, con una advertencia: necesitamos especificar cuantos weis queremos enviar, y no Ethers

# ¿Qué es un Wei?
Un wei es la subunidad más pequeña del Ether — hay 10^18 weis en un ether.

Eso es un montón de ceros para contar — pero afortunadamente Web3.js tiene una utilidad de conversión que hace esto por nosotros.

// Esto va a convertir 1 Eher a Weis
`web3js.utils.toWei("1", "ether");`
En nuestra DApp, configuramos levelUpFee = 0.001 ether, así que cuando llamamos a nuestra función levelUp, podemos hacer que el usuario envíe0.001 Ether junto con la función usando el siguiente código:

```java
cryptoZombies.methods.levelUp(zombieId)
.send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })
```

# Vamos a probarlo
Agreguemos una función levelUp debajo de feedOnKitty. El código va a ser bastante similar a feedOnKitty, pero:

1. La función va a recoger un parámetro, zombieId

2. Pretransacción, debería mostrar el texto de txStatus tal como este: "Leveling up your zombie..."

3. Cuando llama levelUp en el contrato, debería enviar "0.001" ETH convertido a toWei, como en el ejemplo mostrado anteriormente.

4. En caso de éxito, debería mostrar el texto "Power overwhelming! Zombie successfully leveled up"

5. No necesitamos redibujar la UI consultando nuestro contrato inteligente con getZombiesByOwner — porque en este caso sabemos que lo único que ha cambiado es el nivel de un zombi.


```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>
    <div id="txStatus"></div>
    <div id="zombies"></div>
    <script>
      var cryptoZombies;
      var userAccount;
      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
        var accountInterval = setInterval(function() {
          // Comprobar si la cuenta ha sido cambiada
          if (web3.eth.accounts[0] !== userAccount) {
            userAccount = web3.eth.accounts[0];
            // Llamar la función que va a updatear la UI with de la nueva cuenta
            getZombiesByOwner(userAccount)
            .then(displayZombies);
          }
        }, 100);
      }
      function displayZombies(ids) {
        $("#zombies").empty();
        for (id of ids) {
          // Obtener los detalles del zombi de nuestro contrato. Devuelve un objeto `zombie`
          getZombieDetails(id)
          .then(function(zombie) {
            // Se usa las "template literals" (plantillas literales) de ES6 para inyectar variables al HTML.
            // Se adjunta cada uno a nuestro div #zombies
            $("#zombies").append(`<div class="zombie">
              <ul>
                <li>Name: ${zombie.name}</li>
                <li>DNA: ${zombie.dna}</li>
                <li>Level: ${zombie.level}</li>
                <li>Wins: ${zombie.winCount}</li>
                <li>Losses: ${zombie.lossCount}</li>
                <li>Ready Time: ${zombie.readyTime}</li>
              </ul>
            </div>`);
          });
        }
      }
      function createRandomZombie(name) {
        // Esto va a tardar un rato, así que vamos a updatear la UI para que el usuario
        // sepa que la transacción se ha hecho correctamente
        $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
        // Enviar el texto hacia nuestro contrato:
        return cryptoZombies.methods.createRandomZombie(name)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Successfully created " + name + "!");
          // La transacción ha sido aceptada por la blockchain, así que vamos a redibujar la UI
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          // Se avisa al usuario de que su transacción no ha sido completada con éxito
          $("#txStatus").text(error);
        });
      }
      function feedOnKitty(zombieId, kittyId) {
        $("#txStatus").text("Eating a kitty. This may take a while...");
        return cryptoZombies.methods.feedOnKitty(zombieId, kittyId)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Ate a kitty and spawned a new Zombie!");
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }
      function levelUp(zombieId) {
        $("#txStatus").text("Leveling up your zombie...");
        return cryptoZombies.methods.levelUp(zombieId)
        .send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Power overwhelming! Zombie successfully leveled up");
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }
      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }
      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call()
      }
      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call()
      }
      window.addEventListener('load', function() {
        // Aquí se comprueba si Web3.js ha sido inyecto por el navegador (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Usar el proveedor Mist/MetaMask
          web3js = new Web3(web3.currentProvider);
        } else {
          // Aquí se podría poner algo para avisar al usuario de que no tiene Metamask o Mist instalado
          // Probablemente mostrarle un mensake pidiéndole que se lo instale
        }
        // Ahora ya puedes acceder libremente a tu DApp y usar Web3:
        startApp()
      })
    </script>
  </body>
</html>
```
