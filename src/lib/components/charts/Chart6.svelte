<script>
  import ChartBlock from "$components/chart_parts/ChartBlock.svelte";
  import { BasicDropdown, logClickToGA } from "@urbaninstitute/dataviz-components";
  import { LayerCake, Svg } from "layercake";
  import { scaleBand } from "d3";
  import {
    bigDollarFormat,
    percentFormat,
    precisePercentFormat,
    ratioFormat
  } from "$utils/formats.svelte";
  import Bar from "$components/chart_parts/Bar.svelte";
  import { urbanColors } from "@urbaninstitute/dataviz-components/utils";
  let {
    data,
    title = "Here's the title for this chart",
    source = [
      "Here's where we got the data.",
      "Here's where we got the data.",
      "Here's where we got the data."
    ],
    notes = "Here's something you should know about this chart."
  } = $props();

  let filterKey = "Group"; //Banks, credit unions, loan funds
  let chartKey = "Type"; //Median assets etc
  let active_filter = $state("Loan funds");

  let active_source = $derived.by(() => {
    switch (active_filter) {
      case "Loan funds":
        return source[0];
      case "Credit unions":
        return source[1];
      case "Banks":
        return source[2];
      default:
        return "Can't see me";
    }
  });

  let seriesColors = [urbanColors.green, urbanColors.blue];

  let currentData = $derived(data ? data.filter((d) => d[filterKey] == active_filter) : []);

  let filter_names = $derived(
    data ? data.filter((d) => d[chartKey] == data[0][chartKey]).map((d) => d[filterKey]) : [] //Banks, credit unions, loan funds
  );

  let chart_ids = $derived(
    data
      ? data.filter((d) => d[filterKey] == currentData[0][filterKey]).map((d) => d[chartKey])
      : [] //Median assets etc
  );

  let bar_types = $derived(
    data ? Object.keys(data[0]).filter((d) => d != filterKey && d != chartKey) : [] //Latino, Other
  );

  let currentMax = (chartData) => {
    return data ? Math.max(...chartData.map((d) => +d.value)) : 0;
  };

  let currentFormat = (chartID) => {
    return ["Median assets", "Median loans outstanding", "Median net income"].includes(chartID)
      ? bigDollarFormat
      : chartID == "Median self-sufficiency ratio"
        ? ratioFormat
        : chartID == "Median net asset ratio" && active_filter == "Banks"
          ? precisePercentFormat
          : percentFormat;
  };

  let dropdownData = $derived(filter_names.map((d) => ({ label: d, value: d })));
</script>

<ChartBlock {title} description={""} source={active_source} {notes}>
  <br />
  <BasicDropdown
    id="chart-6-dropdown"
    inlineLabel="Select category for chart data"
    bind:value={active_filter}
    variant="primary"
    placeholder={null}
    data={dropdownData}
    dropdownWidth={260}
    on:change={(e) => logClickToGA(e.target, "chart-6-dropdown-click--" + active_filter)}
  />

  <div class="chart-multiple-container">
    <div class="legend">
      <span><span class="box green"></span>Latino-led CDFIs</span>
      <span><span class="box blue"></span>Other CDFIs</span>
    </div>
    {#if data}
      {#each chart_ids as chart_id}
        {@const chartObject = currentData.filter((d) => d[chartKey] == chart_id)[0]}
        {@const chartData = bar_types.map((type) => ({ key: type, value: chartObject[type] }))}
        <div class="chart-multiple">
          <text>{chart_id}</text>
          <div class="chart-area">
            <LayerCake
              padding={{ bottom: 20, right: 50 }}
              x={"value"}
              y={"key"}
              yScale={scaleBand().paddingInner(0.05)}
              xDomain={[0, currentMax(chartData)]}
              data={chartData}
              custom={{
                xKey: "value",
                seriesColors: seriesColors,
                format: currentFormat(chart_id)
              }}
            >
              <Svg>
                <Bar specialBarTransition />
              </Svg>
            </LayerCake>
          </div>
        </div>
      {/each}
    {/if}
  </div>
</ChartBlock>

<style>
  .chart-multiple-container {
    display: grid;
    grid-template: 0.25fr 1fr 1fr / 1fr 1fr;
    column-gap: var(--spacing-10);
    row-gap: var(--spacing--5);
    padding-top: var(--spacing-9);
  }
  .chart-area {
    width: 100%;
    height: 100px;
    @media screen and (max-width: 760px) {
      height: 75px;
    }
  }
  .legend {
    margin-bottom: 20px;
    grid-column: 1 / span 2;
  }
  .box {
    height: 12px;
    width: 12px;
    display: inline-block;
    margin-right: 3px;
  }
  .green {
    background-color: var(--color-green);
  }
  .blue {
    background-color: var(--color-blue);
    margin-left: 12px;
  }
</style>
