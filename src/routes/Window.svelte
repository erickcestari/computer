<script lang="ts">
  import { onMount } from "svelte";

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
      windowElement.style.left = `${e.clientX - offsetX}px`;
      windowElement.style.top = `${e.clientY - offsetY}px`;
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
    windowElement.style.display = "none";
  }

  onMount(() => {
    windowElement.addEventListener("mousedown", onMouseDown);
    document.addEventListener("mousemove", onMouseMove);
    document.addEventListener("mouseup", onMouseUp);

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
      rgba(8, 1, 120, 1) 52%,
      rgba(221, 221, 221, 1) 72%,
      rgba(0, 0, 0, 1) 90%
    );
    user-select: none;
    display: flex;
    justify-content: flex-end;
    align-items: center;
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
    background: #00268e; /* Solid blue background */
    border: 2px solid #ffffff; /* White top and left border */
    border-right-color: #1f3a93; /* Darker blue right border */
    border-bottom-color: #1f3a93; /* Darker blue bottom border */
    color: white; /* White text */
    font-size: 14px; /* Regular font size */
    padding: 4px 6px; /* Padding for the button */
    cursor: pointer;
    font-family: "MS Sans Serif", Arial, sans-serif; /* Classic Windows font */
    box-shadow:
      inset -1px -1px 0px 0px #808080,
      inset 1px 1px 0px 0px #dfe0e1; /* Inner shadow for 3D effect */
  }

  .close-button:active {
    border-color: #1f3a93; /* Darker blue border on press */
    box-shadow:
      inset 1px 1px 0px 0px #808080,
      inset -1px -1px 0px 0px #dfe0e1; /* Inverted shadow for pressed effect */
    background: #3a5dc3; /* Darker blue background on press */
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
