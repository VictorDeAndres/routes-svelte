<script>

	import Alfa from './Alfa.svelte'
	import Bravo from './Bravo.svelte';

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

	let currentSelectedOptionMenu = null;

	function handleClickMenu(selectedOption) {
		console.info(selectedOption);
		currentSelectedOptionMenu = selectedOption;
	}

</script>

<div class="spa">
	<main>
		<ul>
			{#each menuOptions as menu}
				<li>
					<a href="#{menu.url}" on:click={ () => handleClickMenu(menu.id)}>{menu.displayName}</a>
				</li>
			{/each}
		</ul>
	</main>

	<div class="view--container">
		{#if currentSelectedOptionMenu !== null}
			<svelte:component this={menuOptions[currentSelectedOptionMenu].component}/>
		{:else}
			<div>Bienvenido a la página principal</div>
		{/if}
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