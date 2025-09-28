<script lang="ts">
    export let text: string;
</script>

<div class="tag">
    {text}
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .tag {
    background-color: rgba($menu-base-color, 0.5);
    border: 1px solid rgba(255, 255, 255, 0.15);
    color: $menu-text-color;
    font-size: 12px;
    padding: 3px 10px;
    border-radius: 5px;
    margin-left: 10px;
    transition: ease color .2s, ease background-color .2s;
    transform: translateY(-3px);
    font-family: MyCustomFont;
  }
</style>
