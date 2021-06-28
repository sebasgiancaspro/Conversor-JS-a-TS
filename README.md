# Convertidor de js a ts

Pequeña utilidad que escribí en un script para convertir una base de código JS a TypeScript,
al intentar resolver algunos de los errores comunes de TypeScript que se recibirán
sobre tal conversión.

La utilidad realiza las siguientes transformaciones:

1. Cambia el nombre de los archivos `.js` a` .ts`
2. Agrega declaraciones de propiedad a las clases de ES6 para que sean compilables por el Compilador de TypeScript (ver más abajo).
3. Cualquier llamada a función que proporcione menos argumentos que los parámetros declarados en la función hará que los parámetros restantes se marquen como opcionales para esa función. Esto resuelve errores de TS como "Se esperaban 3 argumentos, pero tiene 2 "
   
Nota: debido a que esta utilidad utiliza el servicio de lenguaje TypeScript para realizar las búsquedas para el n. ° 3, puede llevar mucho tiempo ejecutarlo. Para un proyecto pequeño, espere unos minutos. Para un proyecto más grande, podría llevar decenas de minutos. Todavía mucho mejor que los días / semanas que podría llevar arreglar una base de código completa a mano :)
 

Para el n. ° 2 anterior, la utilidad básicamente analiza cualquier propiedad `this` a la que acceda un JS y completa las declaraciones de propiedad de TypeScript adecuadas. Llevar este archivo fuente de entrada `.js` como ejemplo:

```js
class Super {
	someMethod() {
		this.superProp = 1;
	}
}

class Sub extends Super {
	someMethod() {
		this.superProp = 2;
		this.subProp = 2;
	}
}
```


Las clases JS anteriores se reemplazan con las siguientes clases TS:

```ts
class Super {
    public superProp: any;   // <-- added

    someMethod() {
        this.superProp = 1;
    }
}


class Sub extends Super {
    public subProp: any;    // <-- added

    someMethod() {
        this.superProp = 2;
        this.subProp = 2;
    }
}
```

Nota: las propiedades utilizadas cuando `this` se asigna a otra variable también se encontrado con el propósito de crear declaraciones de propiedad. Ejemplo:

```js
class MyClass {
    myMethod() {
        var that = this;
        
        that.something;  // <-- 'something' parsed as a class property
    }
}
```

Para el n. ° 3 anterior, los parámetros se marcan como 'opcionales' cuando hay personas que llaman no los proporcione todos. Por ejemplo, el siguiente JavaScript:

```js
function myFunction( arg1, arg2 ) {
	// ...
}

myFunction( 1 );  // only provide arg1
```

Will be transformed to the following TypeScript:

```ts
function myFunction( arg1, arg2? ) {  // <-- arg2 marked as optional
	// ...
}

myFunction( 1 );
```

## Objetivo

El objetivo de esta utilidad es simplemente hacer que el código `.js` sea compilable bajo el Compilador de TypeScript, simplemente agregue las declaraciones de propiedad escritas como `any` fue la opción más rápida allí. La utilidad puede mirar los inicializadores de propiedad en el futuro para determinar un tipo mejor.

Si tiene otros tipos de errores del compilador que cree que podrían transformado por esta utilidad, no dude en plantear un problema (o extraer ¡pedido!)

Con suerte, solo necesita usar esta utilidad una vez, pero si le ahorró tiempo, por favor, pruébalo para que sepa que te ayudó :)


## Advertencia justa

Esta utilidad realiza modificaciones en el directorio que le pasa. Asegurarse está en un estado limpio de git (u otro VCS) antes de ejecutarlo en caso de que necesite
¡revertir!


## Ejecución de la utilidad desde la CLI

```
npx js-to-ts-converter ./path/to/js/files
```

Si prefiere instalar la CLI de forma global, haga lo siguiente:

```
npm install --global js-to-ts-converter

js-to-ts-converter ./path/to/js/files
```


## Ejecución de la utilidad desde el nodo

TypeScript: 

```ts
import { convertJsToTs, convertJsToTsSync } from 'js-to-ts-converter';


// Async
convertJsToTs( 'path/to/js/files' ).then( 
    () => console.log( 'Done!' ),
    ( err ) => console.log( 'Error: ', err );
); 


// Sync
convertJsToTsSync( 'path/to/js/files' );
console.log( 'Done!' );
```

JavaScript:

```js
const { convertJsToTs, convertJsToTsSync } = require( 'js-to-ts-converter' );


// Async
convertJsToTs( 'path/to/js/files' ).then( 
    () => console.log( 'Done!' ),
    ( err ) => console.log( 'Error: ', err );
); 


// Sync
convertJsToTsSync( 'path/to/js/files' );
console.log( 'Done!' );
```

## Desarrollando

Asegúrate de tener [Node.js](https://nodejs.org) instalado. 

Clone the git repo: 

```
git clone https://github.com/gregjacobs/js-to-ts-converter.git

cd js-to-ts-converter
```

Install dependencias:

```
npm install
```

Run Tests:

```
npm test
```
