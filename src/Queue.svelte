<script>
  import RankChart from "./RankChart.svelte";
  import sum from "lodash/sum";

  export let name;
  export let img;
  export let ranks;
  export let joined;
  export let join;
  export let leave;

  const handleClick = () => {
    if (joined) {
      leave();
    } else {
      join();
    }
  };
</script>

<div class="container" class:active={joined}>
  <div class="card" style="background-image: url({img})" on:click={handleClick}>
    <div class="box">
      {sum(ranks.flat())}
      {sum(ranks.flat()) == 1 ? "player" : "players"}
    </div>
    {#if joined}
      <div class="box center">Leave queue</div>
    {:else}
      <div class="box center">Join queue</div>
    {/if}
    <div class="box">{name}</div>
  </div>

  <div class="chart">
    <RankChart {ranks} />
  </div>
</div>

<style>
  .container {
    box-sizing: border-box;
    display: flex;
    overflow-x: auto;
    height: 355px;
    border: 5px solid transparent;
  }

  .container.active {
    border-color: green;
  }

  .card {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    user-select: none;
    flex-shrink: 0;
    width: 229px;
    height: 100%;
    background-repeat: no-repeat;
    background-size: cover;
  }

  .card:hover {
    cursor: pointer;
  }

  .box {
    width: 100%;
    box-sizing: border-box;
    padding: 8px;
    color: white;
    font-style: italic;
    text-align: right;
    background-color: rgba(0, 0, 0, 0.6);
    background-size: 50% 50%;
    background-position: 50% 50%;
    text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
      1px 1px 0 #000;
    font-size: 2em;
  }

  .center {
    text-align: center;
  }

  .chart {
    background: white;
    flex: auto;
    height: 100%;
  }
</style>
