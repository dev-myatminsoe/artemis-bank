<script>
  import { createEventDispatcher } from "svelte";
  import Record from "./record.svelte";

  const dispatch = createEventDispatcher();

  let visible = false;
  let prefix = "artemis";
  let current_date = null;
  let current_game = "";
  let key_current_game = prefix + "_current_game";
  /**
   * @type {any[]}
   */
  let items = [];

  function start_new_game() {
    current_date = new Date();
    current_game = prefix + "_" + current_date.toISOString();
    items = [];
    localStorage.setItem(key_current_game, current_game);
    localStorage.setItem(current_game, JSON.stringify(items));
    visible = true;
  }

  function game_over() {
    localStorage.setItem(key_current_game, "");
    visible = false;
  }

  function load_recent_game() {
    current_game = localStorage.getItem(key_current_game) || "";

    if (!current_game) {
      let collector = [];
      for (var key in localStorage) {
        if (key.startsWith(prefix) && key != key_current_game) {
          collector.push(key);
        }
      }
      if (collector.length > 0) {
        current_game = collector.reverse()[0];
      }
    }

    if (current_game) {
      items = JSON.parse(localStorage.getItem(current_game) || "[]");
      visible = true;
    }
  }
</script>

<div class="flex flex-row p-2">
  <div>
    <button
      class="bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5"
      on:click={start_new_game}
    >
      New Game
    </button>

    <button
      class="bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5"
      on:click={game_over}>Game over</button
    >

    <button
      class="bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5"
      on:click={load_recent_game}>Load recent game</button
    >
  </div>
</div>

{#if visible}
  <Record {items} {current_game} />
{/if}
