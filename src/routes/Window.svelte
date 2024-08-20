<script lang="ts">
  import { onMount } from "svelte";
  import { createEventDispatcher } from "svelte";
  export let desktopElement: HTMLElement;

  const dispatch = createEventDispatcher();

  let isDragging = false;
  let isResizing = false;
  let resizingEdge:
    | "top"
    | "right"
    | "bottom"
    | "left"
    | "top-left"
    | "top-right"
    | "bottom-left"
    | "bottom-right"
    | null = null;
  let offsetX = 0;
  let offsetY = 0;
  let startWidth = 0;
  let startHeight = 0;
  let windowElement: HTMLElement;
  const grabArea = 30;
  const resizeArea = 20;

  function onMouseDown(e: MouseEvent) {
    const rect = windowElement.getBoundingClientRect();

    if (
      e.clientX > rect.right - resizeArea &&
      e.clientY > rect.bottom - resizeArea
    ) {
      isResizing = true;
      resizingEdge = "bottom-right";
      startWidth = rect.width;
      startHeight = rect.height;
      offsetX = e.clientX;
      offsetY = e.clientY;
    } else if (
      e.clientX < rect.left + resizeArea &&
      e.clientY < rect.top + resizeArea
    ) {
      isResizing = true;
      resizingEdge = "top-left";
      startWidth = rect.width;
      startHeight = rect.height;
      offsetX = e.clientX;
      offsetY = e.clientY;
    } else if (
      e.clientX > rect.right - resizeArea &&
      e.clientY < rect.top + resizeArea
    ) {
      isResizing = true;
      resizingEdge = "top-right";
      startWidth = rect.width;
      startHeight = rect.height;
      offsetX = e.clientX;
      offsetY = e.clientY;
    } else if (
      e.clientX < rect.left + resizeArea &&
      e.clientY > rect.bottom - resizeArea
    ) {
      isResizing = true;
      resizingEdge = "bottom-left";
      startWidth = rect.width;
      startHeight = rect.height;
      offsetX = e.clientX;
      offsetY = e.clientY;
    } else if (e.clientY < rect.top + grabArea) {
      isDragging = true;
      offsetX = e.clientX - rect.left;
      offsetY = e.clientY - rect.top;
    } else {
      resizingEdge = null;
    }
  }

  function onMouseMove(e: MouseEvent) {
    if (isDragging) {
      const desktopRect = desktopElement.getBoundingClientRect();
      const windowRect = windowElement.getBoundingClientRect();
      const newLeft = e.clientX - offsetX;
      const newTop = e.clientY - offsetY;

      if (
        newLeft >= desktopRect.left &&
        newLeft + windowRect.width <= desktopRect.right &&
        newTop >= desktopRect.top &&
        newTop + windowRect.height <= desktopRect.bottom
      ) {
        windowElement.style.left = `${newLeft}px`;
        windowElement.style.top = `${newTop}px`;
      }
    }

    if (isResizing && resizingEdge) {
      let newWidth, newHeight;

      switch (resizingEdge) {
        case "bottom-right":
          newWidth = Math.max(startWidth + (e.clientX - offsetX), 100);
          newHeight = Math.max(startHeight + (e.clientY - offsetY), 100);
          break;
        case "bottom-left":
          newWidth = Math.max(startWidth - (e.clientX - offsetX), 100);
          newHeight = Math.max(startHeight + (e.clientY - offsetY), 100);
          windowElement.style.left = `${e.clientX}px`;
          break;
        case "top-right":
          newWidth = Math.max(startWidth + (e.clientX - offsetX), 100);
          newHeight = Math.max(startHeight - (e.clientY - offsetY), 100);
          windowElement.style.top = `${e.clientY}px`;
          break;
        case "top-left":
          newWidth = Math.max(startWidth - (e.clientX - offsetX), 100);
          newHeight = Math.max(startHeight - (e.clientY - offsetY), 100);
          windowElement.style.left = `${e.clientX}px`;
          windowElement.style.top = `${e.clientY}px`;
          break;
        default:
          return;
      }

      // Constrain resizing within the desktop bounds
      const desktopRect = desktopElement.getBoundingClientRect();
      newWidth = Math.min(
        newWidth,
        desktopRect.right - windowElement.offsetLeft,
      );
      newHeight = Math.min(
        newHeight,
        desktopRect.bottom - windowElement.offsetTop,
      );

      windowElement.style.width = `${newWidth}px`;
      windowElement.style.height = `${newHeight}px`;
    }
  }

  function onMouseUp() {
    isDragging = false;
    isResizing = false;
    resizingEdge = null;
  }

  function closeWindow() {
    dispatch("close");
  }

  onMount(() => {
    windowElement.addEventListener("mousedown", onMouseDown);
    document.addEventListener("mousemove", onMouseMove);
    document.addEventListener("mouseup", onMouseUp);
    windowElement.style.left = `${Math.random() * (desktopElement.clientWidth - 200) + 200}px`;
    windowElement.style.top = `${Math.random() * (desktopElement.clientHeight - 250)}px`;

    return () => {
      windowElement.removeEventListener("mousedown", onMouseDown);
      document.removeEventListener("mousemove", onMouseMove);
      document.removeEventListener("mouseup", onMouseUp);
    };
  });
</script>

<div
  class="window"
  bind:this={windowElement}
  class:drag-cursor={isDragging}
  class:resize-cursor={isResizing}
  class:resize-top-left={resizingEdge === "top-left"}
  class:resize-top-right={resizingEdge === "top-right"}
  class:resize-bottom-left={resizingEdge === "bottom-left"}
  class:resize-bottom-right={resizingEdge === "bottom-right"}
>
  <div class="bar">
    <div>
      <p>Paint</p>
    </div>
    <button class="close-button" on:click={closeWindow}>x</button>
  </div>
  <div class="content">
    <slot />
  </div>
</div>

<style>
  .window {
    position: absolute;
    width: 200px;
    height: 200px;
    border-radius: 50%;
  }

  .bar {
    height: 35px;
    padding: 0 10px;
    border-radius: 5px 5px 0 0;
    background: rgb(10, 0, 171);
    background: linear-gradient(
      0deg,
      rgba(10, 0, 171, 1) 41%,
      rgba(8, 1, 120, 1) 68%,
      rgba(171, 171, 171, 1) 84%,
      rgba(0, 0, 0, 1) 98%
    );
    user-select: none;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .bar p {
    color: white;
    font-size: 14px;
    font-weight: bold;
  }

  .content {
    border: 6px solid rgb(10, 0, 171);
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: white;
    flex: 1;
    width: 100%;
    height: 100%;
  }

  .close-button {
    background: #00268e;
    border: 2px solid #ffffff;
    border-right-color: #1f3a93;
    border-bottom-color: #1f3a93;
    color: white;
    font-size: 14px;
    padding: 4px 6px;
    cursor: pointer;
    font-family: "MS Sans Serif", Arial, sans-serif;
    box-shadow:
      inset -1px -1px 0px 0px #808080,
      inset 1px 1px 0px 0px #dfe0e1;
  }

  .close-button:active {
    border-color: #1f3a93;
    box-shadow:
      inset 1px 1px 0px 0px #808080,
      inset -1px -1px 0px 0px #dfe0e1;
    background: #3a5dc3;
  }

  .drag-cursor {
    cursor: grab;
  }

  .drag-cursor:active {
    cursor: grabbing;
  }

  .resize-cursor {
    cursor: nwse-resize;
  }

  .resize-top-left {
    cursor: nwse-resize;
  }

  .resize-top-right {
    cursor: nesw-resize;
  }

  .resize-bottom-left {
    cursor: nesw-resize;
  }

  .resize-bottom-right {
    cursor: nwse-resize;
  }
</style>
