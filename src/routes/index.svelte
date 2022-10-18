<script>
  import { createEventDispatcher } from "svelte";
  import Record from "./record.svelte";
  import Modal from "../components/Modal.svelte";

  const dispatch = createEventDispatcher();

  let visible = false;
  let prefix = "artemis";
  let current_date = null;
  let current_game = "";
  let show_dialog = false;
  let key_current_game = prefix + "_current_game";
  /**
   * @type {any[]}
   */
  let items = [];

  function on_new_button_click() {
    show_dialog = true;
  }

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
          let tmp_key = key.replace(prefix, "");
          collector.push(tmp_key);
        }
      }
      if (collector.length > 0) {
        let reversed = collector.sort().reverse();
        current_game = prefix + reversed[0];
      }
    }

    if (current_game) {
      items = JSON.parse(localStorage.getItem(current_game) || "[]");
      visible = true;
    }
  }
</script>

<div class="flex flex-row p-2">
  <div class="flex flex-col sm:flex-row w-full">
    <button
      class="w-full sm:w-max bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5 my-1 sm:mx-1"
      on:click={on_new_button_click}
    >
      New Game
    </button>

    <button
      class="w-full sm:w-max bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5 my-1 sm:mx-1"
      on:click={game_over}>Game over</button
    >

    <button
      class="w-full sm:w-max bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5 my-1 sm:mx-1"
      on:click={load_recent_game}>Load recent game</button
    >
  </div>
</div>

{#if show_dialog}
  <Modal
    on:dialog_yes={() => {
      show_dialog = false;
      start_new_game();
    }}
    on:dialog_no={() => (show_dialog = false)}
    >Are you sure to start a new game?</Modal
  >
{/if}

{#if visible}
  <Record {items} {current_game} />
{/if}
