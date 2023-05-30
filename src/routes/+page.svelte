<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;

  let points: { x: number; y: number }[] = [];

  let width = 450;
  let height = 450;
  let pixelRatio = 1;

  onMount(() => {
    const ctx = canvas.getContext("2d");
    if (ctx) {
      draw();
    }
  });

  //   function onMouseMove(event: MouseEvent) {
  //     const ctx = canvas.getContext("2d");
  //     if (ctx) {

  //       // Draw a circle centered on the mouse
  //       ctx.fillStyle = "red";
  //       ctx.beginPath();

  //       //   Canvas coordinates are different from mouse coordinates

  //       ctx.arc(event.clientX, event.clientY, 10, 0, 2 * Math.PI);
  //       ctx.fill();
  //     }
  //   }
  function onMouseDown(event: MouseEvent) {
    points.push({ x: event.clientX, y: event.clientY });

    console.log("mouse down");
    // Draw a circle centered on the mouse
    const ctx = canvas.getContext("2d");
    if (ctx) {
      console.log("painting");
      ctx.fillStyle = "red";
      ctx.beginPath();

      //   Canvas coordinates are different from mouse coordinates

      ctx.arc(event.clientX, event.clientY, 10, 0, 2 * Math.PI);
      ctx.fill();
    }
  }

  function draw() {
    // Draw points
    const ctx = canvas.getContext("2d");
    if (ctx) {
      ctx.fillStyle = "red";
      ctx.beginPath();

      //   Canvas coordinates are different from mouse coordinates
      points.forEach((point) => {
        ctx.arc(point.x, point.y, 10, 0, 2 * Math.PI);
        ctx.fill();
      });
    }
    requestAnimationFrame(draw);
  }
</script>

<svelte:window bind:innerWidth="{width}" bind:innerHeight="{height}" />

<!-- on:mousemove="{onMouseMove}" -->
<canvas
  bind:this="{canvas}"
  width="{width * pixelRatio}"
  height="{height * pixelRatio}"
  class="fs"
  on:mousedown="{onMouseDown}"></canvas>

<style>
  .fs {
    width: 100%;
    height: 100%;
  }
</style>
