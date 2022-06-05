
# Capítulo 13: Comments
¡El código de Solidity para nuestro juego zombie finalmente ha terminado!

En las próximas lecciones, veremos cómo desplegar el código en Ethereum y cómo interactuar con él utilizando Web3.js.

Pero una última cosa antes de que te dejemos ir de la Lección 5. Hablemos de comentar tu código.

# Sintaxis para los comentarios
Comentar en Solidity es como JavaScript. Ya has visto algunos ejemplos de comentarios de una sola línea a lo largo de las lecciones de CryptoZombies:

`// Este es un comentario de una sola línea. Es como una nota para uno mismo (o para otros)`

Simplemente añade // en cualquier lugar y estarás comentando. Es tan fácil que deberías hacerlo todo el tiempo.

Pero te entiendo— a veces una sola línea no es suficiente. ¡Eres escritor, después de todo!

Por lo tanto, también tenemos comentarios de varias líneas:

```s
contract CryptoZombies {
  /* Este es un comentario con múltiples líneas.
      Me gustaría agradecerles a todos vosotros, los que se han tomado su
      tiempo para probar este curso de programación.
      Sé que es gratis para todos ustedes, y se mantendrá libre
    para siempre, pero aún ponemos nuestro corazón y nuestra alma en hacer
    esto tan bueno como puede ser.

    Sepa que este es todavía el comienzo del desarrollo de Blockchain.
    Hemos llegado muy lejos, pero hay muchas maneras de hacer esto en
    comunidad mejor. Si cometimos un error en alguna parte, puedes
    ayudarnos y abrir una pull request aquí:
    https://github.com/loomnetwork/cryptozombie-lessons

    O si tiene algunas ideas, comentarios o simplemente quiere decirnos hola
    - Únase a nuestra comunidad de Telegram en https://t.me/loomnetworkdev
  */
}

```

En particular, es una buena práctica comentar su código para explicar el comportamiento esperado de cada función en su contrato. De esta forma, otro desarrollador (¡O tú, después de un paréntesis de 6 meses en un proyecto!) puede leer rápidamente y comprender a un nivel alto lo que hace su código sin tener que leer el código en sí.

El estándar en la comunidad Solidity es usar un formato llamado natspec, que tiene esta apariencia:


```s
/// @title Un contrato para operaciones matemáticas básicas
/// @author H4XF13LD MORRIS 💯💯😎💯💯
/// @notice Por ahora, este contrato solo añade una función de multiplicar
contract Math {
  /// @notice Multiplica 2 números juntos
  /// @param x el primer uint.
  /// @param y el segundo uint.
  /// @return z el resultado de (x * y)
  /// @dev Esta función actualmente no verifica desbordamientos
  function multiply(uint x, uint y) returns (uint z) {
    // Este es solo un comentario normal, y no será recogido por natspec
    z = x * y;
  }
}
```

@title y @author son simples.

@notice explica a un usuario lo que hace el contrato o la función. @dev es para explicar detalles adicionales a los desarrolladores.

@param y @return son para describir para qué sirve cada parámetro y el valor de retorno de una función.

Tenga en cuenta que no siempre tiene que usar todas estas etiquetas para cada función — todas las etiquetas son opcionales. Pero al menos, deje una nota @dev explicando lo que hace cada función.
