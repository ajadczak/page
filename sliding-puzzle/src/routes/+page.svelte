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

  let height:number = 3, width:number = 3;
  let board:Piece[] = [];

  for (let x = 0; x < width; x++) {
    for(let y = 0; y < height; y++) {
      board.push(new Piece(x, y));
    }
  }

  let gameBoard:HTMLElement;
  onMount(() => {
    gameBoard.style.gridTemplateColumns = '1fr 1fr 1fr';
    gameBoard.style.gridTemplateRows = '1fr 1fr 1fr';
  })

</script>

<main>
  <section class="game" bind:this={gameBoard}>
    {#each board as piece}
      <div class={piece.x === 0 && piece.y === 0 ? "empty" : "piece"}>
      </div>
    {/each}
  </section>
</main>

<style lang="scss">
  :global(body) {
    margin:0;
    padding:0;
    background-color: rgb(113, 113, 113);
  }
  .game {
    display: grid;
  }
  .empty {
    height: 250px;
    width: 250px;
    background-color: rgb(171, 127, 187);
    border: thin solid black;
  }
  .piece {
    height: 250px;
    width: 250px;
    background-color: rgb(72, 23, 90);
    border: thin solid black;
  }
</style>