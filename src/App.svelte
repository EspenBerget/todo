<script>
	import { fade } from 'svelte/transition';
	import { flip } from 'svelte/animate';
	import { onMount } from "svelte";

	let items = []; 
	let uid = 0;
	let todoText;

	const duration = 200; // 200ms animation/transition duration

	onMount(() => {
		 const store = localStorage.getItem('items');
		 const storeIx = localStorage.getItem('ix');
		 items = store !== null ? JSON.parse(store) : [];
		 uid = storeIx !== null ? JSON.parse(storeIx) : 0;
	});

	function save() {
		localStorage.setItem('items', JSON.stringify(items));
		localStorage.setItem('ix', uid);
	}

	function clear() {
		const ok = prompt("Type 'clear all' to delete");
		if (ok === 'clear all') {
			items = [];
			localStorage.clear();
		}
	}

	function remove(id) {
		items = items.filter(item => item.id !== id);
		save();
	}

	window.onkeypress = ev => {
		if (ev.key === 'Enter' && todoText !== "" && todoText !== undefined) {
			items = [{id: uid++, text: todoText, done: false}, ...items];	
			todoText = "";
			save();
		}
	}
</script>

<main>
	<input id="textfield" bind:value={todoText} placeholder="new task?" />
	{#each items as item (item.id)}
		<label class='todo' class:done={item.done} out:fade={{duration: duration / 2}} animate:flip={{ duration: duration }}>
			<input type="checkbox" bind:checked={item.done} on:change={save}>
			{item.text}
			<button on:click={() => remove(item.id)}>X</button>
		</label>
	{/each}
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

	#textfield {
		margin: 1rem;
		width: 50vw;
	}

	.todo {
		border: 1px solid black;
		border-radius: 5px;

		width: 50vw;

		display: flex;
		justify-content: space-between;
		align-items: center;

		padding: 0;
		padding-left: 0.3rem;
		padding-right: 0.3rem;

		background-color: hsl(240, 9%, 93%);

		user-select: none;
		margin: 0.2rem;
	}

	.todo button, .todo input {
		margin: 1rem;
	}

	.done {
		background-color: hsl(240, 1%, 53%);
	}

	.todo button {
		color: crimson;
		background: none;
		border: none;
	} 

	button:hover {
		cursor: pointer;
	}

	#clear {
		position: fixed; 
		top: 1rem;
		left: 1rem;

		background-color: crimson;
		color: white;
		border: 1px groove black;
	}

	button:active {
		border-style: ridge;
	}
</style>