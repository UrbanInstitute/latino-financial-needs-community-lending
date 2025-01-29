<script>
  let {
    text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris velit libero, fringilla at euismod a, pharetra eu augue. Pellentesque porttitor orci ac aliquam hendrerit. Proin efficitur tempor purus, eget elementum elit egestas efficitur. Proin sed purus magna. Sed dapibus arcu at nunc ultrices pulvinar.",
    credit = "Albert Einstein"
  } = $props();
  import QuoteIcon from "$assets/quote.svelte";
</script>

<div class="block-quote-container">
  <div class="top-decoration">
    <div class="quote-icon">
      <QuoteIcon width={40} />
    </div>
    <div class="top-decoration-line"></div>
  </div>

  <p class="block-quote-text">{text}</p>
  <p class="credit">—{credit}</p>
</div>

<style>
  .block-quote-container {
    margin-top: 40px;
    border-bottom: var(--color-gray) 1px solid;
  }
  .top-decoration {
    height: 40px;
    position: relative;
  }
  .top-decoration-line {
    height: 1px;
    width: 100%;
    background-color: var(--color-gray);
    position: absolute;
    top: 20px;
  }
  .quote-icon {
    margin: 0 auto;
    width: 40px;
    z-index: 10;
    position: relative;
  }
  .block-quote-text {
    color: var(--color-gray-shade-darker) !important;
  }
  .credit {
    color: var(--color-gray-shade-darker) !important;
    text-align: left;
    text-transform: uppercase;
    font-size: 14px !important;
  }
</style>
