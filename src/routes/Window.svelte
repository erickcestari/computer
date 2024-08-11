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
  bind:this={windowElement}
  class="window"
  class:drag-cursor={isDragging}
  class:resize-cursor={isResizing}
  class:resize-top-left={resizingEdge === "top-left"}
  class:resize-top-right={resizingEdge === "top-right"}
  class:resize-bottom-left={resizingEdge === "bottom-left"}
  class:resize-bottom-right={resizingEdge === "bottom-right"}
  style="border-top: {grabArea}px solid rgba(0, 0, 0, 0.8);"
>
  <slot />
</div>

<style>
  .window {
    position: absolute;
    width: 800px;
    height: 400px;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: white;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  }

  .window {
    max-width: 100%;
    max-height: 100%;
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
