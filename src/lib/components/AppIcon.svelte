<script lang="ts">
  import { base } from "$app/paths";
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  function openWindow() {
    dispatch("openWindow");
  }
</script>

<button class="app-icon" on:click={openWindow}>
  <img src="{base}/assets/paint.png" alt="App Icon" />
  <p>Paint</p>
</button>

<style>
  .app-icon {
    display: flex;
    flex-direction: column;
    align-items: center;
    cursor: pointer;
    width: 64px;
    background-color: transparent;
    border: none;
  }

  .app-icon img {
    width: 128px;
    height: 128px;
  }

  .app-icon p {
    font-size: 12px;
    text-align: center;
  }
</style>
