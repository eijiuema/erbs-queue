<script>
  import Chart from "chart.js/auto";
  import { onMount, afterUpdate, onDestroy } from "svelte";
  export let ranks;

  let data;

  $: data = {
    labels: ["Ferro", "Bronze", "Prata", "Ouro", "Platina", "Diamante", "Titã"],
    datasets: [
      {
        backgroundColor: [
          "#ccc",
          "#c2835f",
          "#93a7c6",
          "#e9c35f",
          "#63bd98",
          "#a37bcb",
          "#70a1ee",
        ],
        data: ranks[0],
      },
      {
        backgroundColor: [
          "#999",
          "#874e2d",
          "#535c6a",
          "#9c7e2f",
          "#3c8566",
          "#6c4791",
          "#406bac",
        ],
        data: ranks[1],
      },
      {
        backgroundColor: [
          "#aaa",
          "#9b5f3d",
          "#636f83",
          "#b4933e",
          "#489776",
          "#7f59a6",
          "#4c7bc3",
        ],
        data: ranks[2],
      },
      {
        backgroundColor: [
          "#bbb",
          "#ac704d",
          "#7f8fa9",
          "#cda94c",
          "#57a987",
          "#8f68b7",
          "#5d8dd7",
        ],
        data: ranks[3],
      },
    ],
  };

  const options = {
    responsive: true,
    layout: {
      padding: {
        left: 29,
        right: 40,
        top: 36,
        bottom: 15,
      },
    },
    animation: {
      duration: 0,
    },
    maintainAspectRatio: false,
    title: {
      display: false,
    },
    scales: {
      x: { stacked: true },
      y: { stacked: true, suggestedMin: 0, suggestedMax: 18 },
    },
    plugins: {
      legend: {
        display: false,
      },
      tooltip: {
        mode: "index",
        intersect: false,
        callbacks: {
          label: function (context) {
            return `${context.label} ${context.datasetIndex + 1}: ${
              context.formattedValue
            }`;
          },
        },
      },
    },
  };

  let chartRef;
  let chart;

  onMount(async (_) => {
    chart = new Chart(chartRef, {
      type: "bar",
      data,
      options
    });
  });

  afterUpdate(() => {
    if (!chart) return;
    chart.data = data;
    chart.update();
  });

  onDestroy(() => {
    chart = null;
  });
</script>

<!-- <pre style="position: absolute; top: 0; left: 0;">{JSON.stringify(ranks)}</pre>
<pre
  style="position: absolute; top: 2em; left: 0;">{JSON.stringify(players, null, 2)}</pre>
<pre
  style="position: absolute; top: 8em; left: 0;">{JSON.stringify(data, null, 2)}</pre> -->

<canvas bind:this={chartRef} />
