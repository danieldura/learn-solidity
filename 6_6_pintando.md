# Capítulo 6: Mostrando nuestro ejército zombie
Este tutorial no estaría completo si no te enseñamos cómo mostrar realmente los datos que obtiene el contrato.

Sin embargo, de forma realista, querrás utilizar un marco fornt-end como React or Vue.js en tu aplicación web, ya que hacen que tu vida sea mucho más fácil como desarrollador de front-ends. Pero cubrir React o Vue.js está fuera del alcance de este tutorial — eso acabaría siendo un tutorial completo con múltiples lecciones en sí mismo.

Entonces, para mantener a CryptoZombies.io centrado en Ethereum y contratos inteligentes, solo mostraremos un ejemplo rápido en JQuery para demostrar cómo puedes analizar y visualizar los datos que obtienes de tu propio contrato inteligente.

Mostrando los datos de los zombis — un ejemplo como borrador
Hemos agregado un vacío <div id="zombies"></div> en el cuerpo de nuestro documento, así como una función vacía llamada displayZombies.

Recuerda que en la lección anterior llamamos displayZombies desde adentro startApp() con el resultado de una llamada a getZombiesByOwner. Se le pasará una serie de IDs de zombies parecido algo así como:

`[0, 13, 47]`

Por lo tanto, querremos que nuestra función `displayZombies`:

1. Primero borre el contenido del div #zombies, si hay algo adentro. (De esta manera si nuestro usuario cambia su cuenta MetaMask, limpiará su antigua ejército de zombis antes de cargar el nuevo).

2. Pase por cada una id, y para cada uno, llame a getZombieDetails(id) para buscar toda la información sobre ese zombi de nuestro contrato inteligente, después

3. Que coloque la información sobre ese zombie en una plantilla HTML para formatearla y mostrarla, y anexe esa plantilla al div #zombies.

De nuevo, solo estamos usando JQuery aquí, que no tiene un motor de plantillas de forma predeterminada, entonces esto va a ser feo. Pero aquí hay un simple ejemplo de cómo podemos generar estos datos para cada zombi:

```javascript
// Aquí obtenemos todos los detalles de nuestro zombi. Esto devuelve un objeto `zombie`
getZombieDetails(id)
.then(function(zombie) {
  // En esta instacncia usamos los "template literals" (literales de plantilla) de ES6 para insertar variables en el HTML.
  // Adjuntar cada uno a nuestro div #zombies
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

```

# ¿Qué hay de mostrar los sprites zombies?
En el ejemplo anterior, básicamente estamos mostrando el ADN como una cadena. Pero en tu DApp, quieres convertir esto a imágenes para mostrar tu zombie.

Hicimos esto dividiendo la cadena de ADN en subcadenas, y que cada 2 dígitos correspondan a una imagen. Algo así como:

```javascript
// Obtener un integer del 1 al 7 que represente la cabeza de nuestro zombi:
var head = parseInt(zombie.dna.substring(0, 2)) % 7 + 1
// Tenemos 7 imágenes de cabezas de zombis con nombres de archivo secuenciales:
var headSrc = "../assets/zombieparts/head-" + head + ".png"
```

Cada componente se posiciona con CSS usando posicionamiento absoluto, para así superponerlo sobre las otras imágenes.

Si quieres ver nuestra implementación exacta, hemos abierto el componente Vue.js que usamos para la apariencia del zombie, el cual puedes ver aquí (https://github.com/loomnetwork/zombie-char-component). Sin embargo, como hay un montón de código en ese archivo, está fuera del alcance de este tutorial. Para esta lección, nos quedaremos con la implementación de JQuery extemadamente simple mostrada anteriormente, y dejarte que te sumerjas en una implementación más hermosa como tarea 😉

# Vamos a probarlo
Hemos creado una función vacía llamada displayZombies para ti. Vamos a llenarla.

1. Lo primero que queremos hacer es vaciar el div #zombies. En JQuery, puedes hacer esto con $("#zombies").empty();.
2. Después, queremos recorrer todas las IDs, usando un bucle for: for (id of ids) {
3. Dentro del bucle, copia/pega el bloque de código anterior llamado getZombieDetails(id) para cada ID y luego utilizado $("#zombies").append(...) para agregarlo a tu código HTML


```java
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
        // Empieza aquí
        $("#zombies").empty();

        for (id of ids) {
            getZombieDetails(id).then(function(zombie) {
              // En esta instacncia usamos los "template literals" (literales de plantilla) de ES6 para insertar variables en el HTML.
              // Adjuntar cada uno a nuestro div #zombies
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
