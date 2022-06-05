6_5_Metamask_cuentas.md

# Capítulo 5: MetaMask & Cuentas
¡Increíble! Has escrito exitosamente el código front-end para interactuar con tu primer Contrato Inteligente.

Ahora juntemos algunas piezas — digamos que queremos que la página de inicio de nuestra aplicación web muestre todo el ejército zombi de un usuario.

Obviamente, primero necesitaríamos usar nuestra función getZombiesByOwner(owner) para buscar todas las IDs de todos los zombies que los usuarios actuales tienen.

Pero nuestro contrato Solidity espera que owner sea una Solidity address. ¿Cómo podemos saber la dirección del usuario que usa nuestra aplicación?

# Obteniendo la cuenta de los usuarios de MetaMask
MetaMask le permite al usuario administrar múltiples cuentas en su extensión.

Podemos ver qué cuenta está actualmente activa en la variable web3 inyectada a través de:

`var userAccount = web3.eth.accounts[0]`

Como el usuario puede cambiar la cuenta activa en cualquier momento en MetaMask, nuestra aplicación web necesita monitorear esta variable para ver si ha cambiado y actualizar la UI en consecuencia. Por ejemplo, Si la página de inicio del usuario muestra su ejército zombie, cuando cambie su cuenta de MetaMask, querremos actualizar la página para mostrar el ejército zombie de la nueva cuenta que ha seleccionado.

Podemos hacer esto con un buble setInterval de la siguiente manera:


```javascript

var accountInterval = setInterval(function() {
  // Aquí se comprueba si la cuenta ha sido cambiada por otra
  if (web3.eth.accounts[0] !== userAccount) {
    userAccount = web3.eth.accounts[0];
    // Si es así, llamamos una función para que actualize la UI
    updateInterface();
  }
}, 100);
```

Lo que hace es verificar cada 100 milisegundos si userAccount sigue siendo igual a web3.eth.accounts[0] (es decir, el usuario aún tiene la cuenta activa). Si no es así, reasigna userAccount a la cuenta actualmente activa, y llama a una función para actualizar la pantalla.

# Vamos a probarlo
Vamos a hacer que nustra aplicación muestre el ejército de zombies del usuario cuando la página se carga por primera vez, y monitoree la cuenta activa en MetaMask para actualizar la pantalla si cambia.

1. Declara una var llamada userAccount, pero aún no le asignes ningún valor.

2. Al final de startApp(), copia/pega the el código accountInterval del ejemplo de arriba.

3. Sustituye la línea updateInterface(); con una llamada a getZombiesByOwner, y pásalo a userAccount

4. Enlaza una instrucción then después de getZombiesByOwner y pasa el resultado a una función llamada displayZombies. (La sixtaxi es: .then(displayZombies);).

Aún no tenemos la función displayZombies, pero la vamos a implementar en el siguiente capítulo.


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
    <script>
      var cryptoZombies;
      // 1. Declara `userAccount` aquí
      var userAccount;
      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
        // 2. Crea el código `setInterval` justo aquí
        var accountInterval = setInterval(function() {
          // Aquí se comprueba si la cuenta ha sido cambiada por otra
          if (web3.eth.accounts[0] !== userAccount) {
            userAccount = web3.eth.accounts[0];
            // Si es así, llamamos una función para que actualize la UI
            getZombiesByOwner(userAccount).then(displayZombies);
          }
        }, 100);
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
