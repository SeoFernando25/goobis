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

  let lines: Lines[] = []; // Array of lines

  let width = 450;
  let height = 450;
  let lineThickness = 1;

  onMount(() => {
    const ctx = canvas.getContext("2d");
    if (ctx) {
      draw();
    }
  });

  let touching = false;

  function onMouseDown(event: MouseEvent) {
    touching = true;
    lines.push({ x: event.clientX, y: event.clientY, lineThickness });
  }

  function onMouseMove(event: MouseEvent) {
    if (touching) {
      lines[lines.length - 1].endX = event.clientX;
      lines[lines.length - 1].endY = event.clientY;
    }
  }

  function onMouseUp(event: MouseEvent) {
    touching = false;
    lines[lines.length - 1].endX = event.clientX;
    lines[lines.length - 1].endY = event.clientY;
  }

  function onTouchStart(event: TouchEvent) {
    touching = true;
    lines.push({
      x: event.touches[0].clientX,
      y: event.touches[0].clientY,
      lineThickness,
    });
  }

  function onTouchMove(event: TouchEvent) {
    if (touching) {
      lines[lines.length - 1].endX = event.touches[0].clientX;
      lines[lines.length - 1].endY = event.touches[0].clientY;
    }
  }

  function onTouchEnd(event: TouchEvent) {
    touching = false;
    lines[lines.length - 1].endX = event.changedTouches[0].clientX;
    lines[lines.length - 1].endY = event.changedTouches[0].clientY;
  }

  function draw() {
    // Draw points
    const ctx = canvas.getContext("2d");
    if (ctx) {
      if (touching) {
        // Draw preview line
        ctx.beginPath();
        ctx.lineWidth = lineThickness;
        ctx.moveTo(lines[lines.length - 1].x, lines[lines.length - 1].y);
        ctx.lineTo(
          lines[lines.length - 1].endX ?? lines[lines.length - 1].x,
          lines[lines.length - 1].endY ?? lines[lines.length - 1].y
        );
        ctx.stroke();
      }

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
    if (event.ctrlKey && event.key.toLowerCase() === "z") {
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
  on:touchstart="{onTouchStart}"
  on:touchmove="{onTouchMove}"
  on:touchend="{onTouchEnd}"
  on:mousedown="{onMouseDown}"
  on:mousemove="{onMouseMove}"
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
