<script>
  import ChartBlock from "$components/chart_parts/ChartBlock.svelte";
  import { LayerCake, Svg } from "layercake";
  import Bar from "$components/chart_parts/Bar.svelte";
  import TabGroup from "$components/chart_parts/TabGroup.svelte";
  import { scaleBand } from "d3";
  import { dollarFormat, normalFormat } from "$utils/formats.svelte";
  import { urbanColors } from "@urbaninstitute/dataviz-components/utils";

  let {
    data,
    title = "Here's the title for this chart",
    subtitle = ["Dollars subtitle", "Count subtitle"],
    source = "Here's where we got the data.",
    notes = "Here's something you should know about this chart."
  } = $props();

  let group_names = ["Dollars", "Count"];
  let active_name = $state("Dollars");
  let active_var = $derived(active_name == "Dollars" ? "Lending per capita" : "Count per capita");

  let xKey = "All";
  let yKey = "Neighborhood share Latino";
  let yAxisTitle = "Share Latino neighborhoods";
  let currentSubtitle = $derived(active_name == "Dollars" ? subtitle[0] : subtitle[1]);

  let currentFilteredData = $derived(data ? data.filter((d) => d["Category"] == active_var) : []);

  //$inspect(currentFilteredData);
  let yAxisLabels = $derived(currentFilteredData.map((d) => d[yKey]));

  let currentMax = $derived(Math.max(...currentFilteredData.map((d) => +d[xKey])));

  let seriesColors = [
    urbanColors.blue_shade_lighter,
    urbanColors.blue_shade_light,
    urbanColors.blue,
    urbanColors.blue_shade_darker
  ];

  let currentFormat = $derived(active_var == "Count per capita" ? normalFormat : dollarFormat);
</script>

<ChartBlock {title} description={currentSubtitle} {source} {notes}>
  <TabGroup {group_names} bind:active_name chart_number={2} />
  <div class="chart-container">
    {#if currentFilteredData != []}
      <LayerCake
        padding={{ bottom: 15, top: 60, right: 30 }}
        x={xKey}
        y={yKey}
        yScale={scaleBand().paddingInner(0.4)}
        xDomain={[0, currentMax]}
        data={currentFilteredData}
        custom={{ xKey: xKey, seriesColors: seriesColors, format: currentFormat }}
      >
        <Svg>
          <Bar includeYAxisLabels={true} {yAxisLabels} {yAxisTitle} />
        </Svg>
      </LayerCake>
    {/if}
  </div>
</ChartBlock>

<style>
</style>
