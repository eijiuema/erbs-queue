<script>
  import RankChart from "./RankChart.svelte";
  import sum from "lodash/sum";

  export let name;
  export let img;
  export let ranks;
  export let joined;
  export let join;
  export let leave;

  // const secondsToTime = (milliseconds) => {
  //   const seconds = milliseconds / 1000;
  //   const s = String(Math.floor(seconds % 60)).padStart(2, "0");
  //   const m = String(Math.floor((seconds / 60) % 60)).padStart(2, "0");
  //   const h = String(Math.floor(seconds / (60 * 60))).padStart(2, "0");
  //   return `${h}:${m}:${s}`;
  // };

  const handleClick = () => {
    if (joined) {
      leave();
    } else {
      join();
    }
  };
</script>

<div class="container">
  <div class="card" style="background-image: url({img})" on:click={handleClick}>
    <div class="count">
      {sum(ranks.flat())}
      {sum(ranks.flat()) == 1 ? "jogador" : "jogadores"}
    </div>
    {#if joined}
      <div class="joined">
        <div class="check">Pareando...</div>
      </div>
    {:else}
      <div />
    {/if}
    <div class="name">{name}</div>
  </div>

  <div class="chart">
    <RankChart {ranks} />
  </div>
</div>

<style>
  .container {
    display: flex;
    height: 355px;
  }

  .card {
    user-select: none;
    flex-shrink: 0;
    width: 229px;
    height: 100%;
    background-repeat: no-repeat;
    background-size: cover;
    position: relative;
  }

  .card:hover {
    cursor: pointer;
  }

  .name {
    display: inline-block;
    box-sizing: border-box;
    padding: 8px;
    position: absolute;
    bottom: 0;
    width: 100%;
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

  .count {
    display: inline-block;
    box-sizing: border-box;
    padding: 8px;
    position: absolute;
    top: 0;
    width: 100%;
    color: white;
    font-style: italic;
    text-align: right;
    background-color: rgba(0, 0, 0, 0.6);
    background-size: 50% 50%;
    background-position: 50% 50%;
    text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
      1px 1px 0 #000;
    font-size: 1.5em;
  }

  .joined {
    width: 100%;
    height: 50%;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: rgba(0, 0, 0, 0.6);
  }

  .check {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 2em;
    color: white;
    text-align: center;
  }

  .chart {
    background: white;
    flex: auto;
    height: 100%;
  }
</style>
