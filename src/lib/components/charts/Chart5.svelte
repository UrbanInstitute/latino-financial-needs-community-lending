<script>
  import ChartBlock from "$components/chart_parts/ChartBlock.svelte";
  import { LayerCake, Svg } from "layercake";
  import Bar from "$components/chart_parts/Bar.svelte";
  import { scaleBand } from "d3";
  import { percentFormat } from "$utils/formats.svelte";
  import { urbanColors } from "@urbaninstitute/dataviz-components/utils";
  import TabGroup from "$components/chart_parts/TabGroup.svelte";

  let {
    data,
    title = "Here's the title for this chart",
    subtitle = "Here's a subtitle describing the chart.",
    source = "Here's where we got the data."
  } = $props();

  let xKey = "Share Latino-led";
  let secondaryXKey = "Outside PR";
  let yKey = "Primary line of business";

  let button_names = ["All CDFIs", "Loan funds"];
  let active_button = $state("All CDFIs");
  let currentData = $derived(data ? data.filter((d) => d["Tab"] == active_button) : []);
  let seriesNames = $derived(data ? currentData.map((d) => d[yKey]) : [""]);
  let currentMax = $derived(data ? Math.max(...currentData.map((d) => d[xKey])) : []);
  let yAxisLabels = $derived(currentData.map((d) => d[yKey]));
  let yScale = $derived(
    active_button == button_names[0]
      ? scaleBand().paddingInner(0.62)
      : scaleBand().paddingInner(0.53)
  );

  let currentSubtitle = $derived(active_button == "All CDFIs" ? subtitle[0] : subtitle[1]);

  let seriesColors = $derived(
    active_button == "All CDFIs"
      ? [
          urbanColors.blue_shade_darkest,
          urbanColors.blue_shade_darkest,
          urbanColors.blue_shade_darkest,
          urbanColors.blue_shade_darkest
        ]
      : [
          urbanColors.green,
          urbanColors.blue,
          urbanColors.blue,
          urbanColors.blue,
          urbanColors.blue,
          urbanColors.blue,
          urbanColors.blue
        ]
  );
</script>

<ChartBlock {title} description={currentSubtitle} {source} notes={""}>
  <TabGroup group_names={button_names} bind:active_name={active_button} chart_number={5} />
  {#if active_button == "All CDFIs"}
    <div class="legend">
      <span><span class="box blue"></span>Excluding Puerto Rican CDFIs</span>
      <span><span class="box dark"></span>All</span>
    </div>
  {/if}
  <div class="chart-container {active_button == 'Loan funds' ? 'taller' : ''}">
    {#if data}
      <LayerCake
        padding={{ bottom: 30, top: 20, right: 35 }}
        x={xKey}
        y={yKey}
        {yScale}
        xDomain={[0, currentMax]}
        yDomain={seriesNames}
        data={currentData}
        custom={{
          xKey: xKey,
          seriesColors: seriesColors,
          format: percentFormat,
          secondaryXKey: secondaryXKey,
          secondaryColor: urbanColors.blue
        }}
      >
        <Svg>
          <Bar includeYAxisLabels {yAxisLabels} yShift={active_button == "All CDFIs" ? 22 : 0} />
        </Svg>
        {#if active_button == "All CDFIs"}
          <Svg>
            <Bar includeYAxisLabels {yAxisLabels} secondaryData />
          </Svg>
        {/if}
      </LayerCake>
    {/if}
  </div>
</ChartBlock>

<style>
  .taller {
    height: 425px;
  }

  .legend {
    margin-top: 20px;
    grid-column: 1 / span 2;
  }
  .box {
    height: 12px;
    width: 12px;
    display: inline-block;
    margin-right: 3px;
  }
  .blue {
    background-color: var(--color-blue);
  }
  .dark {
    background-color: var(--color-blue-shade-darkest);
    margin-left: 12px;
  }
</style>
