<script>
  import { createEventDispatcher, onDestroy } from "svelte";

  const dispatch = createEventDispatcher();
  const close = () => dispatch("close");
  const dialog_yes = () => {
    dispatch("dialog_yes");
    close();
  };
  const dialog_no = () => {
    dispatch("dialog_no");
    close();
  };

  /**
   * @type {HTMLDivElement}
   */
  let modal;

  const handle_keydown = (e) => {
    if (e.key === "Escape") {
      dialog_no();
      return;
    }

    if (e.key === "Tab") {
      // trap focus
      const nodes = modal.querySelectorAll("*");
      const tabbable = Array.from(nodes).filter((n) => n.tabIndex >= 0);

      let index = tabbable.indexOf(document.activeElement);
      if (index === -1 && e.shiftKey) index = 0;

      index += tabbable.length + (e.shiftKey ? -1 : 1);
      index %= tabbable.length;

      tabbable[index].focus();
      e.preventDefault();
    }
  };

  const previously_focused =
    typeof document !== "undefined" && document.activeElement;

  if (previously_focused) {
    onDestroy(() => {
      previously_focused.focus();
    });
  }
</script>

<svelte:window on:keydown={handle_keydown} />

<div class="modal-background" on:click={close} />

<div class="modal space-y-2" role="dialog" aria-modal="true" bind:this={modal}>
  <slot name="header" />
  <!-- <hr /> -->
  <slot />
  <!-- <hr /> -->

  <!-- svelte-ignore a11y-autofocus -->
  <div class="flex justify-end space-x-2">
    <button
      class="bg-blue-500 text-white hover:bg-blue-700 rounded p-2 px-5"
      autofocus
      on:click={dialog_yes}>Yes</button
    >
    <button
      class="bg-red-500 text-white hover:bg-red-700 rounded p-2 px-5"
      on:click={dialog_no}>No</button
    >
  </div>
</div>

<style>
  .modal-background {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.3);
  }

  .modal {
    position: absolute;
    left: 50%;
    top: 50%;
    width: calc(100vw - 4em);
    max-width: 32em;
    max-height: calc(100vh - 4em);
    overflow: auto;
    transform: translate(-50%, -50%);
    padding: 1em;
    border-radius: 0.2em;
    background: white;
  }
</style>
