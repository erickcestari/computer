<script>
  import Header from "$lib/components/Header.svelte";
  import "../app.css";
</script>

<div class="app">
  <Header />
  <slot />
</div>

<style>
  .app {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    align-items: center;
    background-color: black;
  }
</style>
