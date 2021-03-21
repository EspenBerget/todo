<script>

	import { onMount } from "svelte";
	import TodoItem from './TodoItem.svelte';
	import { fade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	const duration = 200; // 200ms animation/transition duration

	let keys = []; 
	let genKey = 0;

	onMount(() => {
		const storeKeys = localStorage.getItem("keys");
		const storeGenKey = localStorage.getItem("genKey");
		keys = storeKeys ? JSON.parse(storeKeys) : [];
		genKey = storeGenKey ? JSON.parse(storeGenKey) : 0;
	});

	function save() {
		localStorage.setItem("keys", JSON.stringify(keys));
	}

	function clear() {
		const ok = prompt("Type 'clear all' to delete");
		if (ok === 'clear all') {
			keys = [];
			localStorage.clear();
		}
	}

	function remove(filterKey) {
		keys = keys.filter(key => key.id !== filterKey);
		save();
	}

	function newItem() {
		keys = [{id: genKey++}, ...keys];	
		console.log(keys);
		console.log(genKey);
		save();
	}
</script>

<main>
	<button id="new" on:click={newItem}>New</button>
	<div class="container">
		{#each keys as key (key.id)}
		<div animate:flip={{duration}} out:fade={{duration}}>
		<TodoItem key={key.id}>
			<button on:click|stopPropagation={() => remove(key.id)}>X</button>
		</TodoItem>
		</div>
		{/each}
	</div>
	<button id="clear" on:click={clear}>Clear</button>
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		justify-content: flex-start;
		align-items: center;

		width: 100vw;
		height: 100vh;

		background-color: rgb(208, 201, 201);
	}

	.container {
		overflow-y: scroll;
		flex-grow: 2;
	}

	button {
		cursor: pointer;
		margin: 0;
	}

	#new {
		margin-top: 1rem;
		background-color: lightblue;
		border: 1px groove black;
	}

	#clear {
		position: fixed; 
		top: 1rem;
		left: 1rem;

		background-color: crimson;
		color: white;
		border: 1px groove black;
	}
</style>