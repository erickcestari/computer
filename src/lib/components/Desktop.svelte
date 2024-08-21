<script lang="ts">
  import AppIcon from "$lib/components/AppIcon.svelte";
  import WindowComponent from "$lib/components/Window.svelte";

  type Window = {
    id: number;
  };

  let windows: Window[] = [];
  let desktopElement: HTMLElement;

  function handleOpenWindow(): void {
    windows = [...windows, { id: Date.now() }];
  }

  function handleCloseWindow(id: number): void {
    windows = windows.filter((window) => window.id !== id);
  }
</script>

<div class="desktop" bind:this={desktopElement}>
  <AppIcon on:openWindow={handleOpenWindow} />

  {#each windows as window (window.id)}
    {#key window.id}
      <WindowComponent
        on:close={() => handleCloseWindow(window.id)}
        {desktopElement}
      />
    {/key}
  {/each}
</div>

<style>
  .desktop {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 133vh;
    min-height: 100vh;
    background-repeat: no-repeat;
    background-size: contain;
    background-image: url("../lib/images/background.png");
    box-sizing: border-box;
    margin: 0 auto;
  }
</style>
