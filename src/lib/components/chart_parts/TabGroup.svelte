<script>
  import { logClickToGA } from "@urbaninstitute/dataviz-components";

  let {
    group_names = ["First group", "Second group"],
    active_name = $bindable("First group"),
    chart_number = 1
  } = $props();

  function setActive(varname, i, e) {
    active_name = varname;
    logClickToGA(e.target, "chart-" + chart_number + "-select-click--" + e?.target?.textContent);
    return;
  }
</script>

<div class="container" role="radiogroup">
  {#each group_names as group_name, i}
    <button
      class="tab-group-item"
      tabindex={0}
      role="radio"
      aria-checked={active_name == group_name}
      onclick={(e) => setActive(group_name, i, e)}
    >
      {group_name}
    </button>
  {/each}
</div>

<style>
  .container {
    flex: 3 1 350px;
    max-width: 350px;
    min-width: 160px;
    display: flex;
  }
  .tab-group-item {
    height: 45px;
    flex: 1 1 50%;
    appearance: none;
    background: var(--color-gray);
    border: none;
    color: var(--color-black);
    cursor: pointer;
    font-size: 16px;
    line-height: 75%;
    font-family: var(--font-family-sans);
    font-weight: var(--font-weight-bold);
    padding: var(--spacing-4);

    &:hover {
      background: var(--color-blue-shade-lightest);
    }
    &:focus {
      outline: 2px solid var(--color-gray-shade-dark);
    }
  }

  .tab-group-item[aria-checked="true"] {
    color: var(--color-white);
    background: var(--color-blue);
    cursor: initial;
    &:focus {
      outline: 2px solid var(--color-gray-shade-dark);
    }
  }
</style>
