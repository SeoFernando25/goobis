<script lang="ts">
  import { onMount } from "svelte";
  import { listen } from "svelte/internal";

  let canvas: HTMLCanvasElement;

  interface Lines {
    x: number;
    y: number;
    endX?: number;
    endY?: number;
    lineThickness?: number;
  }

  let lines: Lines[] = [];

  let width = 450;
  let height = 450;
  let lineThickness = 1;

  onMount(() => {
    const ctx = canvas.getContext("2d");
    if (ctx) {
      draw();
    }
  });

  function onMouseDown(event: MouseEvent) {
    lines.push({ x: event.clientX, y: event.clientY, lineThickness });
  }

  function onMouseUp(event: MouseEvent) {
    lines[lines.length - 1].endX = event.clientX;
    lines[lines.length - 1].endY = event.clientY;
  }

  function draw() {
    // Draw points
    const ctx = canvas.getContext("2d");
    if (ctx) {
      // Clear screen
      ctx.clearRect(0, 0, width, height);

      //   Draw lines
      //   Set line thickness
      for (let i = 0; i < lines.length; i++) {
        ctx.beginPath();
        ctx.lineWidth = lines[i].lineThickness ?? 1;
        ctx.moveTo(lines[i].x, lines[i].y);
        ctx.lineTo(lines[i].endX ?? lines[i].x, lines[i].endY ?? lines[i].y);
        ctx.stroke();
      }
    }
    requestAnimationFrame(draw);
  }

  //   Bind Ctrl-z to pop
  function onKeyDown(event: KeyboardEvent) {
    if (event.ctrlKey && event.key === "z") {
      lines.pop();
    }
  }
</script>

<svelte:window
  bind:innerWidth="{width}"
  bind:innerHeight="{height}"
  on:keydown="{onKeyDown}"
/>

<!-- Range slider -->
<div class="float-thingy">
  <input type="range" min="1" max="10" step="1" bind:value="{lineThickness}" />
  <p>Line Thickness: {lineThickness}</p>
</div>

<!-- on:mousemove="{onMouseMove}" -->
<canvas
  bind:this="{canvas}"
  width="{width}"
  height="{height}"
  class="fs"
  on:mousedown="{onMouseDown}"
  on:mouseup="{onMouseUp}"></canvas>

<style>
  .fs {
    width: 100%;
    height: 100%;
  }

  .float-thingy {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 100;
    background-color: white;
    padding: 10px;
  }
</style>
