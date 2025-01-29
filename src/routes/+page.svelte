<script>
  import "../app.css"; // import project-specific global styles
  import "@urbaninstitute/dataviz-components/style"; // import _normalize.css and _typography.css (urban-specific classes)
  import * as d3 from "d3";
  import { onMount } from "svelte";
  import {
    Meta,
    TextBlock,
    Block,
    HeadingAlt,
    Headline,
    ProjectCredits
  } from "@urbaninstitute/dataviz-components";
  import Navbar from "$components/Navbar.svelte";
  import { getAbsoluteUrl } from "$lib/utils/urls.js";

  import Chart1 from "$components/charts/Chart1.svelte";
  import Chart2 from "$components/charts/Chart2.svelte";
  import Chart3 from "$components/charts/Chart3.svelte";
  import Chart4 from "$components/charts/Chart4.svelte";
  import Chart5 from "$components/charts/Chart5.svelte";
  import Chart6 from "$components/charts/Chart6.svelte";
  import BlockQuote from "$components/BlockQuote.svelte";
  import { dwKeys } from "$data/chart_4_data.json";
  import archieData from "$data/site_content.aml";

  let data1 = $state(),
    data2 = $state(),
    data3 = $state(),
    data5 = $state(),
    data6 = $state();
  onMount(async () => {
    data1 = await d3.csv("chart_1_data.csv");
    data2 = await d3.csv("chart_2_data.csv");
    data3 = await d3.csv("chart_3_data.csv");
    data5 = await d3.csv("chart_5_data.csv");
    data6 = await d3.csv("chart_6_data.csv");
  });

  let allDataArray = $derived([data1, data2, data3, dwKeys, data5, data6]);
  let allChartBlocks = [Chart1, Chart2, Chart3, Chart4, Chart5, Chart6];

  console.log("👋 Welcome to DataViz@URBAN!");
</script>

<Meta
  title={archieData.meta.title}
  description={archieData.meta.description}
  publishDate={archieData.meta.pub_date}
  socialImage={getAbsoluteUrl("social.jpg")}
/>
<Navbar title={archieData.meta.title} sticky />
<Headline
  headline={archieData.meta.title}
  date={archieData.meta.pub_date}
  eyebrow="Story"
  shareUrl="https://www.urban.org/stories/latino-financial-needs-community-lending"
  width="wide"
/>
<main>
  {#each Object.values(archieData) as blockData}
    {#if blockData.sectionType == "intro"}
      <div class="intro-gradient">
        {#each blockData.text as block, i}
          <TextBlock>
            {#if i == 0}
              <strong style="font-size: 24px;">{@html block.value}</strong>
            {:else}
              {@html block.value}
            {/if}
          </TextBlock>
        {/each}
        <TextBlock>
          <BlockQuote text={blockData.quote_text} credit={blockData.quote_credit} />
        </TextBlock>
      </div>
    {:else if blockData.sectionType == "content"}
      {@const ChartBlockID = allChartBlocks[blockData["sectionIndex"] - 1]}
      {#if blockData.sectionIndex != 1}
        <div class="breaker"></div>
      {/if}
      <div class="content-container">
        <div class="left-side-chart">
          <ChartBlockID
            data={allDataArray[blockData["sectionIndex"] - 1]}
            title={blockData.title}
            subtitle={blockData?.subtitle}
            source={blockData.source}
            notes={blockData?.notes}
          />
        </div>

        <div class="right-side-text">
          {#each blockData.text as block}
            <TextBlock>
              {@html block.value}
            </TextBlock>
          {/each}
          {#if blockData.hasOwnProperty("quote_text")}
            <TextBlock>
              <BlockQuote text={blockData.quote_text} credit={blockData.quote_credit} />
            </TextBlock>
          {/if}
        </div>
      </div>
      {#if blockData.sectionIndex == 6}
        <div class="breaker-last-chart-margin"></div>
      {/if}
    {:else if blockData.sectionType == "outro"}
      <br /><br />
      <Block width="body"><h2>{blockData.title}</h2></Block>
      {#each blockData.text as block}
        <TextBlock>
          {@html block.value}
        </TextBlock>
      {/each}
      <TextBlock>
        <BlockQuote text={blockData.quote_text} credit={blockData.quote_credit} />
        <br />
        {blockData.bullet_intro}
        <ul class="outro-points">
          {#each blockData.bullets as block}
            <li>
              <strong>{block.title}</strong>
              {@html block.text}
            </li>
          {/each}
        </ul>
        {blockData.last_graf}
      </TextBlock>

      <Block width="body">
        <div class="breaker-section"></div>
        <HeadingAlt content={"About the data"} />
      </Block>
      {#each blockData.about as block}
        <TextBlock>
          {@html block.value}
        </TextBlock>
      {/each}
      <div class="breaker-small"></div>
      <ProjectCredits heading="Project credits" items={blockData.credits}
        ><TextBlock slot="intro"
          ><span style="font-style: italic;">{@html blockData.credits_intro}</span></TextBlock
        ></ProjectCredits
      >
      <TextBlock>
        <p>{@html blockData.view_on_git_text}</p>
      </TextBlock>
    {/if}
  {/each}
</main>

<style>
  .breaker {
    height: 340px;
    display: block;

    @media screen and (max-width: 1190px) {
      height: 120px;
    }
  }
  .breaker-section {
    height: 60px;
    border-top: 1px var(--color-gray) solid;
    margin-top: 128px;
    display: block;
  }
  .breaker-small {
    height: 90px;
  }
  .breaker-last-chart-margin {
    height: 112px;

    @media screen and (max-width: 1190px) {
      height: 42px;
    }
  }

  .intro-gradient {
    background: linear-gradient(180deg, #fff 10.5%, #f5f5f5 100%);
    padding-top: 128px;
    padding-bottom: 80px;
    margin-bottom: 240px;

    @media screen and (max-width: 1190px) {
      margin-bottom: 112px;
    }
  }
  .content-container {
    display: grid;
    position: relative;
    grid-template-columns: 600px 116px 1fr;
    max-width: 1190px;
    margin: 0 auto;

    @media screen and (max-width: 1190px) {
      display: block;
    }
  }
  .left-side-chart {
    grid-column-start: 1;
    position: sticky;
    align-self: start;
    top: var(--spacing-20);

    @media screen and (max-width: 1190px) {
      position: static;
      margin-bottom: 42px;
    }
  }
  .right-side-text {
    grid-column-start: 3;
  }
  .outro-points > li {
    list-style-type: none;
    margin-left: 40px;
    margin-bottom: 36px;
  }
</style>
