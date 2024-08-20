<script>
  import Header from "./Header.svelte";
  import "../app.css";
</script>

<div class="app">
  <Header />

  <main>
    <slot />
  </main>
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

  main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 134vh;
    min-height: 100vh;
    background-repeat: no-repeat;
    background-size: contain;
    background-image: url("../lib/images/background.png");
    box-sizing: border-box;
    margin: 0 auto;
  }
</style>
