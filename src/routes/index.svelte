<script>
    import { onMount } from "svelte";
    import { prevent_default } from "svelte/internal";

    /**
     * @type {any[]}
     */
    let items = [];

    let name = "";
    let amount = 200;

    function formDecrement() {
        amount -= 100;
    }

    function formIncrement() {
        amount += 100;
    }

    onMount(() => {
        items = JSON.parse(localStorage.getItem("items") || "[]");
    });

    function save() {
        let item = {
            id: Date.now(),
            name: name,
            amount: amount,
        };
        items = [...items, item];
        name = "";
        amount = 200;
        saveToLocal();
    }

    function del(id) {
        items = items.filter((item) => item.id !== id);
        saveToLocal();
    }

    function saveToLocal() {
        localStorage.setItem("items", JSON.stringify(items));
    }
</script>

<div class="grid m-2 items-center gap-2">
    {#each items as item}
        <div class="flex gap-1">
            <input
                type="text"
                class="bg-gray-100 rounded p-1 w-full"
                bind:value={item.name}
                on:change={saveToLocal}
            />
            <button
                on:click={() => {
                    item.amount -= 100;
                    saveToLocal();
                }}
                class="bg-blue-500 text-white hover:bg-blue-700 px-4 rounded"
                >-</button
            >
            <input
                class="bg-gray-100 rounded w-16 text-center text-sm"
                type="number"
                bind:value={item.amount}
            />
            <button
                on:click={() => {
                    item.amount += 100;
                    saveToLocal();
                }}
                class="bg-blue-500 text-white hover:bg-blue-700 px-4 rounded"
                >+</button
            >
            <button
                on:click={() => {
                    del(item.id);
                }}
                class="bg-red-500 text-white rounded hover:bg-red-700 px-5"
            >
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="h-6 w-6"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke="currentColor"
                    stroke-width="2"
                >
                    <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
                    />
                </svg>
            </button>
        </div>
    {/each}

    <hr class="mt-4" />

    <form on:submit|preventDefault={save} class="flex gap-1">
        <input
            type="text"
            class="bg-gray-100 rounded p-1 w-full"
            bind:value={name}
        />
        <button
            on:click={formDecrement}
            class="bg-blue-500 text-white hover:bg-blue-700 px-4 rounded"
            >-</button
        >
        <input
            class="bg-gray-100 rounded p-1 w-16 text-center text-sm"
            bind:value={amount}
        />
        <button
            on:click={formIncrement}
            class="bg-blue-500 text-white hover:bg-blue-700 px-4 rounded"
            >+</button
        >
        <button
            on:click={save}
            class="bg-green-500 rounded px-5 text-white hover:bg-green-700"
        >
            <svg
                xmlns="http://www.w3.org/2000/svg"
                class="h-6 w-6"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
                stroke-width="2"
            >
                <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M12 9v3m0 0v3m0-3h3m-3 0H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z"
                />
            </svg>
        </button>
    </form>
</div>
