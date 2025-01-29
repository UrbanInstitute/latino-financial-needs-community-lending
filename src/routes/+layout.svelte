<script>
  import { Theme, FontsUrban, Analytics } from "@urbaninstitute/dataviz-components";
</script>

<FontsUrban />
<Analytics title="latino-financial-needs-community-lending" mode={import.meta.env.MODE} />
<Theme>
  <slot />
</Theme>
