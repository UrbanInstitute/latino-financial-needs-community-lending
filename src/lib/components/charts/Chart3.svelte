<script>
  import { BasicDropdown, logClickToGA } from "@urbaninstitute/dataviz-components";
  import ChartBlock from "$components/chart_parts/ChartBlock.svelte";
  import { LayerCake, Svg } from "layercake";
  import Bar from "$components/chart_parts/Bar.svelte";
  import TabGroup from "$components/chart_parts/TabGroup.svelte";
  import { scaleBand } from "d3";
  import { dollarFormat, normalFormat, preciseDollarFormat } from "$utils/formats.svelte";
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

  let active_button = $state("Micro");

  let xKey = $derived(active_button);
  let yKey = "Neighborhood share Latino";
  let yAxisTitle = "Share Latino neighborhoods";
  let currentSubtitle = $derived(
    active_name == "Dollars"
      ? active_button + " " + subtitle[0]
      : "Number of " +
          active_button[0].toLowerCase() +
          active_button.substring(1) +
          " " +
          subtitle[1]
  );

  let button_names = $derived(
    data
      ? Object.keys(data[0]).filter((d) => !group_names.includes(d) && d != yKey && d != "Category")
      : []
  );

  let currentFilteredData = $derived(data ? data.filter((d) => d["Category"] == active_var) : []);
  let yAxisLabels = $derived(currentFilteredData.map((d) => d[yKey]));

  let currentMax = $derived(Math.max(...currentFilteredData.map((d) => +d[active_button])));

  let seriesColors = [
    urbanColors.blue_shade_lighter,
    urbanColors.blue_shade_light,
    urbanColors.blue,
    urbanColors.blue_shade_darker
  ];

  let currentFormat = $derived(
    active_var == "Count per capita"
      ? normalFormat
      : active_button == "Single family"
        ? dollarFormat
        : preciseDollarFormat
  );

  let dropdownData = $derived(button_names.map((d) => ({ label: d, value: d })));
  let dropWidth = $state(0);
  //dropdownWidth={175}
</script>

<ChartBlock {title} description={currentSubtitle} {source} {notes}>
  <div class="inline">
    <TabGroup {group_names} bind:active_name chart_number={3} />
    <div class="dropdown-wrapper" bind:clientWidth={dropWidth}>
      <BasicDropdown
        id="chart-3-dropdown"
        inlineLabel="Select category for chart data"
        bind:value={active_button}
        variant="primary"
        placeholder={null}
        data={dropdownData}
        on:change={(e) => logClickToGA(e.target, "chart-3-dropdown-click--" + active_button)}
      />
    </div>
  </div>

  <div class="chart-container">
    {#if currentFilteredData != []}
      <LayerCake
        padding={{ bottom: 15, top: 60, right: 40 }}
        x={xKey}
        y={yKey}
        yScale={scaleBand().paddingInner(0.4)}
        xDomain={[0, currentMax]}
        data={currentFilteredData}
        custom={{ xKey: xKey, seriesColors: seriesColors, format: currentFormat }}
      >
        <Svg>
          <Bar includeYAxisLabels {yAxisLabels} {yAxisTitle} />
        </Svg>
      </LayerCake>
    {/if}
  </div>
</ChartBlock>

<style>
  .inline {
    display: flex;
    gap: 18px;
    flex-wrap: nowrap;
    @media screen and (max-width: 400px) {
      flex-wrap: wrap;
    }
  }
  .dropdown-wrapper {
    flex: 1 3 auto;
  }
  :global(#chart-3-dropdown) {
    height: 45px;
    width: 100% !important;
    min-width: 175px;
  }
  :global(.dropdown-container:has(#chart-3-dropdown)) {
    width: auto !important;
  }
</style>
