<script>
	// import Cards from './Cards.svelte';
	import { onMount } from 'svelte';
	import axios from 'axios';

	let pokemonFound = 0;
	const getPokemonCount = async() => {
		axios.get('https://pokeapi.co/api/v2/pokemon?limit=151')
			.then(response => {
				const data = response.data.results;
				const names = data.map(pokemon => pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1));

				console.log("names", names);
				pokemonFound = names;
			})
			.catch(error => {
				console.log(error);
			});
	}

	onMount(() => {
		console.log("onMount");
		getPokemonCount();
	});

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
			<button class="bold">Show filters</button>
			<button class="bold">Random</button>
		</div>
	</section>
	<section class="results">

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
	display: block;
	height: 100vh;
	width: 100%;
	background: #7fad71;
}

</style>
