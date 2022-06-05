# Capítulo 3: Hablando a los contratos
Ahora que ya hemos inicializado Web3.js con el proveedor Web3 de MetaMask, vamos a configurarlo para hablar con nuestro contrato inteligente.

Web3.js va a necesitar dos cosas para poder hablar con nuestro contrato inteligente: su address y su ABI.

# Dirección del contrato
Después de terminar de escribir tu contrato inteligente, lo tendrás de compilar y desplegar en Ethereum. Vamos a cubrir el despliegue en la siguiente lección, pero ya que es un proceso bastante diferente de escribir código, hemos decidido desordenar y cubrir primero Web3.js.

Después de implementar tu contrato, obtiene una dirección fija en Ethereum donde vivirá por siempre. Si recuerda de la Lección 2, la dirección del contrato CryptoKitties en el mainnet de Ethereum es 0x06012c8cf97BEaD5deAe237070F9587f8E7A266d.

Deberás copiar esta dirección después de la implementación para poder hablar con su contrato inteligente..

# Contrato ABI
La otra cosa que Web3.js necesitará para hablar con tu contrato es su ABI. Básicamente es una representación de los métodos de sus contratos en formato JSON que le dice a Web3.js cómo formatear las llamadas de función de una manera que su contrato lo entienda.

Cuando to compilas tu contrato para implementarlo en Ethereum (que cubriremos en la Lección 7), el compilador de Solidity le dará el ABI, por lo que deberá copiar y guardar esto además de la dirección del contrato.

Como aún no hemos cubierto en tema de la implementación y de momento no lo vamos a hacer, para esta lección, hemos compilado el ABI para usted y lo hemos puesto en un archivo llamado cryptozombies_abi.js, almacenado en una variable almacenada cryptoZombiesABI.

Si incluimos cryptozombies_abi.js en nuestro proyecto, vamos a ser capaces de acceder a nuestro CryptoZombies ABI esando esta variable.

# Instanciando un Contrato Web.js
Una vez que ya tengas la dirección de su contrato y su respectiva ABI, ya podrás crear una instancia en Web3 de la siguiente manera:
```s
// Instantiate myContract
var myContract = new web3js.eth.Contract(myABI, myContractAddress);
```

# Vamos a probarlo
1. En el <head> de nuestro documento, incluye otra etiqueta de script para cryptozombies_abi.js para que podamos importar nuestro ABI en el proyecto.

2. Al comienzo de nuestra etiqueta <script> en <body>, declara una var llamada cryptoZombies, pero no le asignes ningún valor. Más tarde usaremos esta variable para almacenar nuestro contrato instanciado.

3. Después, crea una function llamada startApp(). Vamos a completar el cuerpo en los próximos 2 pasos.

Lo primero que debe hacer startApp() es declarar una var llamada cryptoZombiesAddress y asígnale su valor al string "YOUR_CONTRACT_ADDRESS" (esta es la dirección del contrato de CryptoZombies en la mainnet).

Finalmente, vamos a inicializar nuestro contrarto. Establezca cryptoZombies igual a un nuevo web3js.eth.Contract como lo hicimos en el código de ejemplo anterior. (Usando cryptoZombiesABI, que se importa con nuestra etiqueta de script, y cryptoZombiesAddress desde arriba).



```js
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
      // 2. Empieza el código aquí

      var cryptoZombies;

      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
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