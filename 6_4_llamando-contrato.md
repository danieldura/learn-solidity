# Capítulo 4: Llamando a las funciones de nuestro Contrato
¡Nuestro contrato está todo listo! Ahora podemos usar Web3.js para hablar con él.

Web3.js tiene dos métodos que utilizaremos para llamar funciones en nuestro contrato: call y send.


# Call
Es usado para funciones view and pure. Solamente se ejecuta en el nodo local, así que no creará una transacción en la Blockchain.

Revisión: las funciones view y pure son de solo lectura y no cambian de estado en la cadena de bloques. Tampoco cuestan nada de gas, así que no necesitarán firmar ninguna transacción con MetaMask. Usando Web3.js, deberías call una función llamada myMethod con el parámetro 123 de la siguiente manera:

myContract.methods.myMethod(123).call()


# Send
send creara una transacción y cambiará los datos de la Blockchain. Deberás usar send para cualquier función que no sea view o pure.

Nota: sending una transacción va a requerir al usuario pagar gas, y aparecerá su Metamask para solicitarle que firme su transacción. Cuando usamos Metamask como nuestro proveedor web3, todo esto sucede automáticamente cuando llamamos a send(), Así que no necesitaremos hacer nada especial en nuestro código. ¡Genial! Usando Web3.js, para enviar una transacción llamada myMethod con el parámetro 123 se haría de la siguiente forma:

`myContract.methods.myMethod(123).send()`
La sintaxis es casi idéntica a call().


# Obteniendo los datos de nuestros zombies
Ahora veamos un ejemplo real de usar call para acceder a los datos de nuestro contrato.

Recuerda que hicimos nuestro array de zombies public:

Zombie[] public zombies;
En Solidity,cuando tú declaras una variable public, automáticamente se crea una función pública "getter" con el mismo nombre. Entonces, si quisieras buscar el zombi con la id 15, lo llamarías como si fiera la función zombies(15).

Así es como escribiríamos una función de JavaScript en nuestro front-end que tomaría una identificación de zombie, consultar nuestro contrato para ese zombie, y devolver el resultado:


Nota: Todos los ejemplos de código que estamos usando en esta lección usan la versión 1.0 de Web3.js, que usa promesas en lugar de devoluciones. Muchos otros tutoriales que verá en internet usarán una versión anterior de Web3.js. La sintaxis ha cambiado mucho con la versión 1.0, así que si estás copiando el código de otros tutoriales, asegúrate de que estén usando la misma versión que ti.

```javascript
function getZombieDetails(id) {
  return cryptoZombies.methods.zombies(id).call()
}
// Aquí llamamos la función para después con el .then hacer algo con el resultado:
getZombieDetails(15)
.then(function(result) {
  console.log("Zombie 15: " + JSON.stringify(result));
});

```

Veamos lo que está pasando aquí.

cryptoZombies.methods.zombies(id).call() se comunicará con el nodo proveedor de Web3 y le pedirá que le devuelva el índice id de Zombie[] public zombies en nuestro contrato.

Ten en cuenta que esto es asincrónico, como una llamada API a un servidor externo. Entonces Web3 devuelve una promesa aquí. (Si no está familiarizado con las promesas de JavaScript ... ¡Es hora de hacer un poco de tarea adicional antes de continuar!)

Una vez que la promesa se resuelva (lo que significa que recibimos una respuesta del proveedor de web3), nuestro código de ejemplo continúa con la declaración then, que registra result en la consola.

result va a ser un objeto JavaScript parecido a esto:
```json
{
  "name": "H4XF13LD MORRIS'S COOLER OLDER BROTHER",
  "dna": "1337133713371337",
  "level": "9999",
  "readyTime": "1522498671",
  "winCount": "999999999",
  "lossCount": "0" // Obviamente.
}
```

Podríamos tener una lógica de interfaz para analizar este objeto y mostrarlo de manera significativa en el front-end.


# Vamos a probarlo
We've gone ahead and copied getZombieDetails into the code for you.

Vamos a crear una función similar a zombieToOwner. Si recuerdas de ZombieFactory.sol, teníamos un mapping que parecía algo así:

mapping (uint => address) public zombieToOwner;
Define una función JavaScript llamada zombieToOwner. Similar a getZombieDetails anterior, tomará un id como parámetro, y devolverá una call Web3.js a zombieToOwner en nuestro contrato.

Debajo de esto, crea una tercerá función para getZombiesByOwner. Si recuerdas de ZombieHelper.sol, la función se veía así:

function getZombiesByOwner(address _owner)
Nuestra función getZombiesByOwner tomará owner como parámetro, y devolverá una llamada (call) a getZombiesByOwner.

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
      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
      }
      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }
      // 1. Define `zombieToOwner` aquí
      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call();
      }

      // 2. Define `getZombiesByOwner` aquí
      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call();
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
