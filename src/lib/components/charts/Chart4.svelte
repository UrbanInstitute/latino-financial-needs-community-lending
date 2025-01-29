<script>
  import ChartBlock from "$components/chart_parts/ChartBlock.svelte";
  import TabGroup from "$components/chart_parts/TabGroup.svelte";
  import Toggle from "$components/chart_parts/Toggle.svelte";
  import {
    BasicDropdown,
    DatawrapperIframe,
    logClickToGA
  } from "@urbaninstitute/dataviz-components";
  let {
    data,
    title = "Here's the title for this chart",
    subtitle = [
      "Here's a DOLLARS subtitle describing the STATE chart.",
      "Here's a COUNT subtitle describing the STATE chart.",
      "Here's a DOLLARS subtitle describing the MSA chart.",
      "Here's a COUNT subtitle describing the MSA chart."
    ],
    source = "Here's where we got the data.",
    notes = "Here's something you should know about this chart."
  } = $props();

  //$inspect("chart 4 data", data);

  let active_button = $state("All");
  let active_geogs = ["State data", "MSA data"];
  let isMSAActiveToggle = $state(false);
  let MSAorState = $derived(isMSAActiveToggle ? "MSA" : "State");
  let group_names = ["Dollars", "Count"];
  let active_name = $state("Dollars");
  let active_var = $derived(active_name == "Dollars" ? "Lending per capita" : "Count per capita");
  let currentSubtitle = $derived.by(() => {
    if (isMSAActiveToggle) {
      if (active_name == "Dollars") return subtitle[2];
      return subtitle[3];
    } else {
      if (active_name == "Dollars") return subtitle[0];
      return subtitle[1];
    }
  });

  let button_names = $derived(data[MSAorState][active_var].map((d) => d.name));

  //$inspect("dwKeys", data, data[MSAorState], data[MSAorState][active_var]);
  let currentData = $derived(data[MSAorState][active_var]);

  let active_chart_key = $derived(currentData.find((d) => d.name == active_button).key);

  let dropdownData = $derived(button_names.map((d) => ({ label: d, value: d })));
</script>

<ChartBlock {title} description={currentSubtitle} {source} {notes}>
  <div class="inline">
    <TabGroup {group_names} bind:active_name chart_number={4} />
    <Toggle labels={active_geogs} bind:active={isMSAActiveToggle} />
  </div>
  <div class="breaker"></div>
  <BasicDropdown
    id="chart-4-dropdown"
    inlineLabel="Select category for chart data"
    bind:value={active_button}
    variant="primary"
    placeholder={null}
    data={dropdownData}
    dropdownWidth={260}
    on:change={(e) => logClickToGA(e.target, "chart-4-dropdown-click--" + active_button)}
  />

  <DatawrapperIframe title="" ariaLabel={title} datawrapperId={active_chart_key} height={"500"} />
</ChartBlock>

<style>
  .inline {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 18px;
  }
  .breaker {
    height: 15px;
  }
</style>
