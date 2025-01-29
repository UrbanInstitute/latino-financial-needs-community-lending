<script>
  import ChartBlock from "$components/chart_parts/ChartBlock.svelte";
  import { LayerCake, Svg } from "layercake";
  import { scaleBand } from "d3";
  import Column from "$components/chart_parts/Column.svelte";
  import AxisX from "$components/chart_parts/AxisX.svelte";
  import { dollarFormat } from "$utils/formats.svelte";
  import { urbanColors } from "@urbaninstitute/dataviz-components/utils";
  let {
    data,
    title = "Here's the title for this chart",
    subtitle = "Here's a subtitle describing the chart.",
    source = "Here's where we got the data.",
    notes = "Here's something you should know about this chart."
  } = $props();

  let xKey = "Neighborhood majority";
  let yKey = "All";

  let seriesColors = [
    urbanColors.blue,
    urbanColors.blue,
    urbanColors.blue,
    urbanColors.green,
    urbanColors.blue,
    urbanColors.blue
  ];

  let innerWidth = $state();
  let paddingBottom = $derived(innerWidth < 630 ? 60 : 40);

  let true_data = $derived(innerWidth < 630 && data ? updateDataLabels(data) : data);

  function updateDataLabels(old_data) {
    let new_data = //[{ "Neighborhood majority": "All of everything", All: 140 }];
      old_data.map((d) => ({
        "Neighborhood majority": d[xKey]
          .toString()
          .replace("No", "No\n")
          .replace("AN and NH", "AN\nand\nNH"),
        All: d[yKey]
      }));

    return new_data;
  }

  let seriesNames = $derived(data ? true_data.map((d) => d[xKey]) : [""]);
</script>

<svelte:window bind:innerWidth />
<ChartBlock {title} description={subtitle} {source} {notes}>
  <div class="chart-container">
    {#if data}
      <LayerCake
        padding={{ bottom: paddingBottom }}
        x={xKey}
        y={yKey}
        xScale={scaleBand().paddingInner(0.15)}
        xDomain={seriesNames}
        yDomain={[0, 83]}
        data={true_data}
        custom={{ seriesColors: seriesColors, format: dollarFormat }}
      >
        <Svg>
          <AxisX gridlines={false} tickMarks={false} yTick={25} />
          <Column showLabels />
        </Svg>
      </LayerCake>
    {/if}
  </div>
</ChartBlock>

<style>
</style>
