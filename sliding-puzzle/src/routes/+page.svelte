<script lang="ts">
  import {onMount} from 'svelte';

  class Piece {
    x: number;
    y: number;

    constructor(x:number, y: number) {
      this.x = x;
      this.y = y;
    }
  }

  let imageUrl = "https://images.unsplash.com/photo-1682685794304-99d3d07c57d2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDF8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=686&q=80";
  let height:number = 3, width:number = 3;
  let board:Piece[] = [];
  for (let x = 0; x < width; x++) {
    for(let y = 0; y < height; y++) {
      board.push(new Piece(x, y));
    }
  }

  let imageSrc:HTMLImageElement;
  let canvasSrc:HTMLCanvasElement;
  let gameBoard:HTMLElement;
  onMount(() => {
    gameBoard.style.gridTemplateColumns = `repeat(${width}, auto)`;
    gameBoard.style.gridTemplateRows = `repeat(${height}, auto)`;
  });

  function useImageFromCamera() {
    navigator.mediaDevices.getUserMedia({
      audio: false,
      video: true,
    }).then((stream) => {
      
    })
  }

  $: imageSrc;
  $: yOffset = imageSrc?.naturalWidth / width;
  $: xOffset = imageSrc?.naturalHeight / height;
</script>

<header>
  <button on:click={() => useImageFromCamera()}>
    Use camera
  </button>
</header>

<main>
  <img class="source-image" src={imageUrl} bind:this={imageSrc} alt="source game" />
  <canvas class="offscreen-canvas" bind:this={canvasSrc}></canvas>
  <section class="game" bind:this={gameBoard}>
      {#each board as piece, i (i)}
        {#if i === 0}
          <div class="empty"></div>
        {:else}
          <div
            class="piece"
            style={`
              background: url(${imageUrl}) ${piece.x * xOffset}px ${piece.y * yOffset}px;
              height: ${imageSrc?.naturalHeight / height};
              width: ${imageSrc?.naturalWidth / width}''
            `}
          />
        {/if}
      {/each}
  </section>
</main>

<style lang="scss">
  :global(html) {
    margin:0;
    padding:0;
  }
  :global(body) {
    margin:0;
    padding:0;
    background-color: rgb(113, 113, 113);
  }
  .source-image {
    display: none;
  }
  .offscreen-canvas {
    position: absolute;
    top: 0;
    left: -10000px;
    height: 800;
    width: 640;
  }
  .game {
    display: grid;
    height: 100vh;
    width: 100%;
  }
  .empty {
    background-color: rgb(171, 127, 187);
    border: thin solid black;
  }
  .piece {
    background-color: rgb(72, 23, 90);
    border: thin solid white;

  }
</style>