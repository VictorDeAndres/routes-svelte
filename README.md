# Crea tu propio "routing" en Svelte

Esta es una copia del post ([routes-svelte](https://victordeandres.es/post/routes-svelte)) que puedes encontrar en mi blog https://victordeandres.es

# Breve introducción a Svelte

Sí estás leyendo este post, entiendo que ya conoces Svelte un nuevo frontend framework o librería, que está teniendo bastante aceptación entre la comunidad de desarrolladores y creo que tiene un gran potencial. 

Aún así haré una breve introducción de este nuevo framework/librería. 

Svelte tiene muchos conceptos similares a los tres otros grandes frontend frameworks: React, Angular y Vue. Si ya conoces alguno de ellos empezar a trabajar con él no te será complejo. Pero Svelte tiene una gran diferencia con ellos. Svelte tiene que ser **compilado**. A diferencia de los otros frameworks los cuales no es necesario compilarlos para poder ejecutarlos. Pueden funcionar directamente en un navegador, aunque habitualmente generamos bundles o paquetes para mejorar el rendimiento de nuestros programas.

Aunque los desarrollos con Svelte están realizados con HTML, Javascript y CSS, este código no es compresible para los navegadores si no es compilado previamente. Este paso es obligatorio antes de publicar nuestra app, puedes entenderlo como una desventaja o como una ventaja. Yo lo entiendo como una ventaja respecto al resto de sus competidores, ya que al compilar nuestro código se realiza una optimización que incrementará el rendimiento de nuestra aplicación.

No quiero extenderme mucho más en esta introducción, ya que el motivo principal de este post es explicar cómo desarrollar una emulación de llamadas a rutas en una app escrita con Svelte.

Si quieres conocer un poco más en profundidad que es Svelte te recomiendo que navegues a su página web [svelte](https://svelte.dev/) donde podrás conocerlo más en profundidad. 

# Inicio

Svelte no tiene un gestor de rutas propio, y tampoco existe un standard de facto. Aunque en [npm](https://www.npmjs.com/) puedes encontrar distintas librerías que te proporcionan esta funcionalidad, en esta ocasión vamos a desarrollar nuestro gestor de rutas.

El funcionamiento de nuestro gestor de rutas es muy sencillo. Imaginemos que estamos desarrollando una página spa donde en la parte superior tenemos un menú, y queremos ir variando el cuerpo de nuestra página según las distintas opciones de nuestro menú. 

#Página principal

El fichero app.svelte, que es nuestra vista principal sería como sigue:

```html
<script>
	const menuOptions = [
		{
			id: 0,
			displayName: 'Opcion Alfa',
			url: 'alfa'
		},{
			id: 1,
			displayName: 'Opcion Bravo',
			url: 'bravo'
		}
	]
</script>

<div class="spa">
	<main>
		<ul>
			{#each menuOptions as menu}
				<li>
					<a href="#{menu.url}">{menu.displayName}</a>
				</li>
			{/each}
		</ul>
	</main>

	<div class="view--container">
		Bienvenido a la página principal
	</div>
</div>

<style>

	.spa {
		display: flex;
		flex-direction: column;
	}

	ul {
		list-style: none;
	}

	li {
		float: left;
		margin: 0 2em;
		font-size: 1.5em;
	}

	.view--container {
		margin: 2em 4em;
	}
	
</style>
```

Como verás no hay nada reseñable en este código. Simplemente he creado un objeto Json que contendrá las opciones de nuestro menú. Para en el momento de la visualización poder crear un loop y poder mostrar las distintas opciones de nuestro menú.

Si ejecutamos nuestro proyecto en este momento obtendremos el siguiente resultado.

![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/lsypnjpumtd2hz9rea9t.png)

Es una página muy sencilla. Simplemente tenemos un menú con dos opciones, y un mensaje de bienvenida

#Las vistas

El siguiente paso que realizaremos será crear el código de las vistas de nuestras opciones de menú. Para ello crearemos dos nuevos ficheros Alfa.svelte 

```html
<script>
</script>

<h3>Hey !!!!</h3>>
<p>Esta es la página de la primera opción</p>>

<style>
  h3 {
    color: green;
  }

  p {
    color: rebeccapurple;
  }
</style>
```

y Bravo.svelte

```html
<script>
</script>

<h3>Enhorabuena</h3>>
<p>El emulador de rutas funciona</p>>

<style>
  h3 {
    color: blue;
  }

  p {
    color: royalblue;
  }
</style>
```

Son dos vistas muy sencillas que nos permitirán testar que nuestro emulador de rutas para svelte funciona correctamente. 

# Capturar los clicks en el menú

Si ahora pulsamos sobre cualquiera de las opciones de menú lo único que observaremos será como varía la dirección de nuestro navegador, haciendo referencia a una nueva url, pero no veremos ningún cambio en nuestra vista. 

Para desarrollar nuestro emulador de rutas lo primero será capturar los clicks en nuestras opciones del menú. Para ello utilizaremos el evento click de svelte que nos permite llamar a una función predefinida por nosotros. Además vamos a pasar como parámetro de nuestra función el id de la opción seleccionada.

Para ello haremos las siguientes modificaciones: Primero añadiremos el siguiente código en nuestro bloque script.

```javascript
function handleClickMenu(selectedOption) {
    console.info(selectedOption);
}
```

Por el momento simplemente mostraremos por la consola el id de la opción seleccionada. 

Y en nuestro código html sustituiremos el código de nuestro link por el siguiente:

```html
<a href="#{menu.url}" on:click={ () => handleClickMenu(menu.id)}>{menu.displayName}</a>
```

Con esta modificación estamos indicando que cada vez que hagamos un click en cualquiera de las opciones del menú llamaremos a la función handleClickMenu enviado como único parámetro el identificador de la opción.

#Cambio de vista.

Una vez hemos capturado los clicks el siguiente paso que vamos a desarrollar es el cambio de nuestra vista. De este forma simularemos el cambio de ruta en nuestra aplicación.

Lo primero que haremos será importar nuestros componentes en nuestro componente principal.

```javascript
import Alfa from './Alfa.svelte'
import Bravo from './Bravo.svelte';
```

Una vez hemos importados nuestras componentes modificare nuestro objeto menuOptions, creando una nueva propiedad, **component**, qué hará referencia a un componente en concreto, el que visualizaremos cuando se seleccione una opción. 

Siendo la definición del objeto menuOptions como sigue:

```javascript
const menuOptions = [
	{
		id: 0,
		displayName: 'Opcion Alfa',
		url: 'alfa',
		component: Alfa
	},{
		id: 1,
		displayName: 'Opcion Bravo',
		url: 'bravo',
		component: Bravo
	}
]
```

El siguiente paso es indicar que cada vez que hagamos click en cualquiera de nuestras opciones de menú se cargue el componente indicado.

Para ello creare una nueva variable en nuestro desarrollo, currentSelectedOptionMenu, que será donde se almacenará el id seleccionado  , para poder mostrar el componente correspondiente posteriormente. Este paso lo realizaremos en la función **handleClickMenu** que creamos anteriormente.

La función quedará de la siguiente forma:

```javascript
function handleClickMenu(selectedOption) {
	currentSelectedOptionMenu = selectedOption;
}
```

He borrado el mensaje por consola por que ya no nos es necesario.

Y el último paso es modificar la vista para que muestre el componente seleccionado. Para ello modificaremos el código html correspondiente al bloque, **view--container**.

El nuevo código sería:

```html
<div class="view--container">
	{#if currentSelectedOptionMenu !== null}
		<svelte:component this={menuOptions[currentSelectedOptionMenu].component}/>
	{:else}
		<div>Bienvenido a la página principal</div>
	{/if}
</div>
```

Voy a comentar más detalladamente esta parte. 

Lo primero que vemos es una condición que nos indica que cuando el valor currentSelectedOptionMenu no sea nulo no muestre un componente, y en caso contrario muestre el texto "Bienvenido a la página principal". Pero donde se hace la "magia" es en la línea:

```javascript
<svelte:component this={menuOptions[currentSelectedOptionMenu].component}/>
```

En esta línea hacemos uso de la etiqueta <svelte:component>. Esta etiqueta de la api de svelte nos proporciona la funcionalidad de renderizar componentes dinámicamente. Y nosotros la utilizamos para renderizar un nuevo componente, el componente correspondiente a la propiedad component de nuestro objeto **menuOptions** de la opción previamente seleccionada.

# Conclusión.

Como verás hemos construido nuestro propio gestor de rutas de una manera muy sencilla. El código que hemos visto en esta ocasión es bastante sencillo.

Pero a partir de este punto podemos seguir definiendo nueva funcionalidad para nuestras vistas. Por ejemplo crear unas guardas para proporcionar seguridad a nuestra página web. 
