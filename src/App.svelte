<script>
	import { triggerRequestPokemon } from "./request";
	import { onMount } from 'svelte';
	import axios from 'axios';

	let pokemonCount = 0; // Number of pokemon in the API

	let offset = -20; // Offset starts at -20 since the data is 20 pokemon behind
	let loadedPokemons = [];
	let isFetching = false;

	// Executed when the page loads
	onMount(async() => {
		console.log("The page is mounted!");
		// Get the total number of pokemon then get the first 20
		await getPokemonCount().then(() => {
			console.log("pokemonCount", pokemonCount);
		});

		// Get the first 20 pokemon
		loadMorePokemons();
	});

	// Get the total number of pokemon from the API
	const getPokemonCount = async() => {
		await axios.get('https://pokeapi.co/api/v2/pokemon')
			.then(response => {
				return pokemonCount = response.data.count;
			})
			.catch(error => {
				console.log(error);
			});
	}

	// Fetch the next 20 pokemon
	const loadMorePokemons = async() => {
		try {
			isFetching = true;
			const response = await axios.get('https://pokeapi.co/api/v2/pokemon', {
				params: {
					offset: offset += 20,
				}
			}).then (response => {
				// Add a uppercase first letter to the pokemon name
				response.data.results.forEach(pokemon => {
					pokemon.name = pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
				});
				loadedPokemons = [...loadedPokemons, ...response.data.results];
				isFetching = false;
			});

			console.log("loadedPokemons", loadedPokemons);
		} catch (error) {
			console.log(error);
		}
	}

	const loadMore = () => {
		setTimeout(() => {
			loadMorePokemons();
		}, 100);
	}

	// Reactive declarations
	let elementRef = null;
	$: {
		if (elementRef && loadedPokemons.length < pokemonCount) {
			triggerRequestPokemon({ fetch: loadMore, element: elementRef });
		}
	}

</script>

<main>
	<section class="landing flex">
		<h1 class="bold">Svelte-Pokedex</h1>
		<p>A simple pokedex interface built with
			<a class="bold" href="https://svelte.dev/" target="_blank" rel="noreferrer">Svelte</a> and
			<a class="bold" href="https://pokeapi.co/" target="_blank" rel="noreferrer">PokeAPI</a>
		</p>
		<input type="search" name="searchbar" placeholder="Looking for a specific pokemon?" />
		<div class="buttons flex">
			<button class="bold" on:click={() => console.log("Show filters")}>Show filters</button>
			<button class="bold">Random</button>
		</div>
	</section>

	<section class="results">
		{#each loadedPokemons as pokemon, i}
			<div class="card">
				<p>{i+1} {pokemon.name}</p>
				{#if i < 905}
					<img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/{i+1}.png" alt="{pokemon.name}">
				{:else}
					<img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/0.png" alt="{pokemon.name}">
				{/if}
			</div>
		{/each}

		{#if isFetching === false && loadedPokemons.length < pokemonCount}
			<li bind:this={elementRef}>isFetching...</li>
		{/if}
	</section>
</main>

<style>

main {
	display: grid;
	place-items: center;
	height: 100vh;
	width: 100%;
}

section.landing {
	position: relative;
	top: -100px;
	height: 100vh;
	text-align: center;
	text-transform: uppercase;
	flex-direction: column;
}

section.landing h1 {
	margin-bottom: 2rem;
}

section.landing p {
	text-transform: none;
	margin: 0;
}

a.bold {
	padding: 0 1rem;
}


input[name="searchbar"] {
	height: 75px;
	margin: 3rem auto 1.5rem auto;
}

.buttons {
	margin: 25px;
}

.buttons > button {
	text-transform: uppercase;
	font-weight: bold;
}

section.results {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
	place-items: center;
	grid-gap: 1rem 0.5rem;
	width: 100%;
	background: #7fad71;
}
</style>
