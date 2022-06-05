# Capítulo 7: Enviando transacciones
¡Increíble! Ahora nuestra UI va a detectar la cuenta MetaMask del usuario, y automáticamente mostrar por pantalla su ejército zombie en la página de inicio.

Ahora veamos el uso de las funciones send para cambiar los datos en nuestro contrato inteligente.

1. Hay algunas diferencias importantes en las funciones call:

sending (enviando) una transacción requiere una from address de quien está llamando la función (que se convierte en msg.sender en nuestro códio Solidity). Queremos que este sea el usuario de nuestra DApp, para así que MetaMask pueda aparecer en la pantalla para solicitar al usuario que firme la transacción.

2. sending (enviando) cuesta gas.

3. Habrá un retraso significativo desde que el usuario envía una transacción y cuando esa transacción realmente tenga efecto en la Blockchain. Esto se debe a que tenemos que esperar a que la transacción se incluya en un bloque, y el block time para Ethereum es, en promedio, 15 segundos. Si hay muchas transacciones pendientes en Ethereum o si el usuario envía un precio de gas demasiado bajo, nuestra transacción puede tener que esperar varios bloques para ser incluida, y esto podría tomar varios minutos.

Por lo tanto, necesitaremos lógica en nuestra aplicación para manejar la naturaleza de la asincronía de este código.


# Creando zombis
Veamos un ejemplo con la primera función en nuestro contrato que un nuevo usuario llamará: createRandomZombie.

Para recordar esta función, te dejamos es código Solidity de dicha función:

```s
function createRandomZombie(string _name) public {
  require(ownerZombieCount[msg.sender] == 0);
  uint randDna = _generateRandomDna(_name);
  randDna = randDna - randDna % 100;
  _createZombie(_name, randDna);
}

```


Aquí hay un ejemplo de cómo podríamos llamar a esta función en Web3.js usando MetaMask:

```java
function createRandomZombie(name) {
  // Como esto va a tardar un poco, vamos a actualizar la UI cuando la transacción
  // se haya completado correctamente.
  $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
  // Enviar el texto a nuestro contrato:
  return cryptoZombies.methods.createRandomZombie(name)
  .send({ from: userAccount })
  .on("receipt", function(receipt) {
    $("#txStatus").text("Successfully created " + name + "!");
    // Si la transacción es aceptada por la blockchain, vamos a redibujar la UI
    getZombiesByOwner(userAccount).then(displayZombies);
  })
  .on("error", function(error) {
    // Si la transacción no se produce correctamente, vamos a mostrar por pantalla el error
    $("#txStatus").text(error);
  });
}

```

Nuestra función envía una transacción a nuestro proveedor de Web3 y encadena algunos event listeners:

- receipt se activará cuando la transacción se incluya en un bloque en Ethereum, lo que significa que nuestro zombie ha sido creado y guardado en nuestro contrato.
- error se activará si hay un problema que impide que la transacción se incluya en un bloque, como que el usuario no envíe suficiente gas. Queremos informar al usuario en nuestra IU de que la transacción no se realizó para que puedan volver a intentarlo.

Nota: Opcionalmente puedes especificar gas y gasPrice cuando llamas send, por ejemplo .send({ from: userAccount, gas: 3000000 }). Si no especificas esto, MetaMask va a permitir al propio usuario a especificar los valores.


# Vamos a probarlo
Hemos añadido un div con la ID txStatus — de esta manera podemos usar este div para actualizar al usuario con mensajes con el estado de nuestras transacciones.

Abajo de displayZombies, copia/pega el código de la función createRandomZombie situado arriba.

Vamos a implementar otra función feedOnKitty.

La lógica para llamar feedOnKitty será casi idéntica — enviaremos una transacción que llame a la función, y una transacción exitosa resulta en un nuevo zombie creado para nosotros, así que querremos volver a dibujar la IU después de que sea exitosa.

Crea una copia de createRandomZombie justo debajo de ella, pero haz las siguientes modificaciones:

a) Asigna el nombre feedOnKitty a la segunda función, la cual va a coger dos parámetros: zombieId y kittyId

b) El texto #txStatus debería actualizarse a: "Eating a kitty. This may take a while..."

c) Haz que llame a feedOnKitty en tu contrato, y pásale los mismos dos argumentos.

d) El mensaje de éxito de #txStatus debería ser: "Ate a kitty and spawned a new Zombie!"


```javascript

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
          // Check if account has changed
          if (web3.eth.accounts[0] !== userAccount) {
            userAccount = web3.eth.accounts[0];
            // Call a function to update the UI with the new account
            getZombiesByOwner(userAccount)
            .then(displayZombies);
          }
        }, 100);
      }
      function displayZombies(ids) {
        $("#zombies").empty();
        for (id of ids) {
          // Look up zombie details from our contract. Returns a `zombie` object
          getZombieDetails(id)
          .then(function(zombie) {
            // Using ES6's "template literals" to inject variables into the HTML.
            // Append each one to our #zombies div
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
        // Como esto va a tardar un poco, vamos a actualizar la UI cuando la transacción
        // se haya completado correctamente.
        $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
        // Enviar el texto a nuestro contrato:
        return cryptoZombies.methods.createRandomZombie(name)
        .send({ from: userAccount })
          .on("receipt", function(receipt) {
            $("#txStatus").text("Successfully created " + name + "!");
            // Si la transacción es aceptada por la blockchain, vamos a redibujar la UI
          getZombiesByOwner(userAccount).then(displayZombies);
          })
          .on("error", function(error) {
          // Si la transacción no se produce correctamente, vamos a mostrar por pantalla el error
          $("#txStatus").text(error);
        });
      }
      function feedOnKitty(zombieId, kittyId) {
        $("#txStatus").text("Eating a kitty. This may take a while...");
        return cryptoZombies.methods.feedOnKitty(zombieId, kittyId)
        .send({ from: userAccount })
          .on("receipt", function(receipt) {
          $("#txStatus").text("Ate a kitty and spawned a new Zombie!");
            // Si la transacción es aceptada por la blockchain, vamos a redibujar la UI
          getZombiesByOwner(userAccount).then(displayZombies);
          })
          .on("error", function(error) {
          // Si la transacción no se produce correctamente, vamos a mostrar por pantalla el error
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
        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }
        // Now you can start your app & access web3 freely:
        startApp()
      })
    </script>
  </body>
</html>

```