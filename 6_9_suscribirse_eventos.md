6_9_suscribirse_eventos.md

# Capítulo 9: Suscribirse a los eventos
Como puedes ver, interactuar con tu contrato inteligente a través de Web3.js es bastante sencillo — una vez que hayas configurado tu entorno, llamando funciones (call) y enviando transacciones (send) no es tan diferent de una API normal.

Hay un aspecto más que queremos cubrir — suscribirse a los eventos de nuestro Smart Contract.


# Escuchando para nuevos Zombis
Si recuerdas nustro contrato zombiefactory.sol, escribimos un evento llamado NewZombie que se ejecuta cada vez que se creaba un nuevo zombie:

`event NewZombie(uint zombieId, string name, uint dna);`

En Web3.js, puedes suscribirte a un evento, por lo que tu proveedor Web3 desencadena cierta lógica en su código cada vez que se ispara:

```java
cryptoZombies.events.NewZombie()
.on("data", function(event) {
  let zombie = event.returnValues;
  // Podemos acceder a los 3 objetos de este evento mediante el objeto `event.returnValues`:
  console.log("A new zombie was born!", zombie.zombieId, zombie.name, zombie.dna);
}).on("error", console.error);
```

Tenga en cuenta que esto activaría una alerta cada vez que se creara un zombie en nuestra DApp — no solo para el usuario actual. ¿Y si solo queremos alertas para el usuario actual?


# Usando indexed
Para filtrar eventos y solo escuchar cambios relacionados con el usuario actual, nuestro contrato Solidity tendría que usar la palabra clave indexed, como lo hicimos en el evento Transfer de nuestra implementación de ERC721:

`event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);`
En este caso, porque _from y _to son indexed, eso significa que podemos filtrar por ellos en nuestro event listener de nuestro front-end:

```java
// Use `filter` to only fire this code when `_to` equals `userAccount`
cryptoZombies.events.Transfer({ filter: { _to: userAccount } })
.on("data", function(event) {
  let data = event.returnValues;
  // ¡El usuario actual acaba de recibir un zombi!
  // Haz algo aquí para actualizar la UI y mostrarlo
}).on("error", console.error);

```
Como puedes ver, el uso de los campos events e indexed puede ser una práctica bastante útil para escuchar los cambios en tu contrato y reflejarlos en el front-end de tu aplicación.

# Consultando eventos pasados
Incluso podemos consultar eventos pasados usando getPastEvents, y usar los filtros fromBlock y toBlock para darle a Solidity un rango de tiempo para los registros de eventos ("block" en este caso, se refiere al número de bloque de Ethereum):

```java
cryptoZombies.getPastEvents("NewZombie", { fromBlock: 0, toBlock: "latest" })
.then(function(events) {
  // `events` is an array of `event` objects that we can iterate, like we did above
  // Este código nos dará una lista de cada zombie que se haya creado
});

```
Como puede usar este método para consultar los registros de eventos desde el principio de los tiempos, esto presenta un caso de uso interesante: Usar eventos como una forma de almacenamiento más económica.

Si recuerdas, guardar datos en la blockchain es una de las operaciones más costosas de Solidity. Pero usar eventos es mucho más barato en términos de gas.

La compensación aquí es que los eventos no son legibles desde el propio contrato inteligente. Pero es un caso de uso importante a tener en cuenta si tiene datos que desea que se registren históricamente en la blockchain para que pueda leerlos desde el front-end de su aplicación.

Por ejemplo, podríamos usar esto como un registro histórico de batallas de zombies — podríamos crear un evento por cada vez que un zombie ataque a otro y quien gane. El contrato inteligente no necesita estos datos para calcular los resultados futuros, pero son datos útiles para que los usuarios puedan navegar desde el front-end de la aplicación.

# Vamos a probarlo
Vamos a agregar un código para escuchar el evento Transfer y actualizar la interfaz de usuario de nuestra aplicación si el usuario actual recibe un nuevo zombie.

Tendremos que agregar este código al final de la función startApp para así asegurarse de que el contrato cryptoZombies se haya inicializado antes de agregar un detector de eventos.

1. Debajo de startApp(), copia/pega el bloque de código para escuchar cryptoZombies.events.Transfer

2. Para que la línea actualice la interfaz de usuario, usa getZombiesByOwner(userAccount).then(displayZombies);

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
        // Empieza aquí
        cryptoZombies.events.Transfer({ filter: { _to: userAccount } })
          .on("data", function(event) {
            let data = event.returnValues;
            getZombiesByOwner(userAccount).then(displayZombies);
          }).on("error", console.error);
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
        .send({ from: userAccount, value: web3.utils.toWei("0.001", "ether") })
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