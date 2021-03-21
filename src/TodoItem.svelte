<script>
    import marked from 'marked';
    import { onDestroy, onMount } from "svelte";

    export let key;
    let text = "";
    let done = false;
    let editMode = false;


    function save() {
        localStorage.setItem(key, JSON.stringify({text, done}));
    }

    function load() {
        const item = localStorage.getItem(key);
        if (item) {
            const obj = JSON.parse(item);
            text = obj.text;
            done = obj.done;
        }
    }

    onMount(load);
    onDestroy(remove);

    function remove() {
        localStorage.removeItem(key);
    }

    function toggleEdit() {
        editMode = !editMode;
        done = false;
        if (!editMode) { // Save when exit edit mode
            save();
        }
    }

    function toggleDone() {
        if (!editMode) {
            done = !done;
            save();
        }
    }

</script>

<div class="todo" class:done on:click={toggleDone}>
    {#if editMode}
    <textarea bind:value={text}></textarea>
    {:else}
    <div class="markedText">{@html marked(text)}</div>
    {/if}
    <div class="controls">
        <slot></slot>
        <button on:click|stopPropagation={toggleEdit}>Edit</button>
        <button on:click|stopPropagation={save}>Save</button>
    </div>
</div>

<style>
    .todo {
        width: 50vw;
        border: 1px solid black;

        display: flex;
        margin: 1rem;
        padding: 0.2rem;

        background-color: hsl(5, 23%, 91%);
    }

    .controls {
        align-self: flex-start;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;

    }

    .done {
        background-color: rgb(65, 179, 107);
        height: 101px;

        overflow: hidden;
    }

    textarea {
        margin: 0;
        max-height: 500px;
        min-height: 500px;
        height: auto;
        flex-grow: 2;
        resize: none;
    }

    textarea:focus {
        outline: none !important;
    }

    .markedText {
        overflow: wrap;
        user-select: none;
        flex-grow: 2;
    }

    button {
        cursor: pointer;
        margin: 0;
    }

    button:active {
        background-color: #fff;
    }

</style>