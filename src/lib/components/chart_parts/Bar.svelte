<!--
  @component
  Generates an SVG bar chart.
 -->
<script>
  import { getContext } from "svelte";
  import { fade } from "svelte/transition";

  const { data, xGet, yGet, xScale, yScale, custom } = getContext("LayerCake");

  export let value_placement = "outer"; //also accepts "inner"
  export let includeYAxisLabels = false; //turns on labels just above the bars, left-aligned
  export let yAxisLabels = [];
  export let yAxisTitle = "";
  export let specialBarTransition = false;
  export let secondaryData = false;
  export let yShift = 0;
</script>

{#if yAxisTitle != ""}
  <text class="yaxis-title" x={0} y={0 - 30}>{yAxisTitle}</text>
{/if}
<g class="bar-group">
  {#each $data as d, i (specialBarTransition ? i : yAxisLabels[i].slice(-10).toLowerCase())}
    <g in:fade={{ duration: 100, delay: 200 }}>
      <g in:fade={{ delay: 250, duration: 250 }}>
        {#if includeYAxisLabels}
          {#key yAxisLabels[i]}
            <text in:fade={{ delay: 100, duration: 250 }} class="yaxis-label" x={0} y={$yGet(d) - 7}
              >{yAxisLabels[i]}</text
            >
          {/key}
        {/if}
      </g>
      <rect
        class="group-rect"
        data-id={i}
        x={$xScale.range()[0]}
        y={$yGet(d) + yShift}
        height={$yScale.bandwidth()}
        width={secondaryData ? $xScale(d[$custom.secondaryXKey]) : $xGet(d)}
        fill={secondaryData ? $custom.secondaryColor : $custom.seriesColors[i]}
      ></rect>
      <g in:fade={{ delay: 250, duration: 250 }}>
        {#key d + d[$custom.xKey]}
          <text
            class="label"
            in:fade={{ delay: 100, duration: 250 }}
            x={secondaryData ? $xScale(d[$custom.secondaryXKey]) + 5 : $xGet(d) + 5}
            y={$yGet(d) + $yScale.bandwidth() / 2 + 7 + yShift + (secondaryData ? -8 : 0)}
            text-anchor={value_placement == "outer" ? "start" : "end"}
            >{$custom.format.format(
              secondaryData ? d[$custom.secondaryXKey] : d[$custom.xKey]
            )}</text
          >
        {/key}
      </g>
    </g>
  {/each}
</g>

<style>
  rect {
    transition: ease-in-out 250ms;
  }
  .yaxis-label {
    font-size: 16px;
    font-weight: 700;
  }
  .yaxis-title {
    font-size: 16px;
    font-weight: 700;
    font-style: italic;
  }
</style>
