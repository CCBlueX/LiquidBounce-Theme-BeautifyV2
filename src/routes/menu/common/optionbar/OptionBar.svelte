<script lang="ts">
    import {fly} from "svelte/transition";
</script>

<div class="option-bar" in:fly|global={{duration: 180, x: 40}}>
    <slot />
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .option-bar {
    background: rgba($menu-base-color, 0.75);
    padding: 15px 20px;
    display: flex;
    align-items: center;
    border-radius: 7.5px;
    column-gap: 30px;
    margin-bottom: 20px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
    font-family: MyCustomFont;

    @media (max-width: 1400px) {
      column-gap: 15px;
    }
  }
</style>
