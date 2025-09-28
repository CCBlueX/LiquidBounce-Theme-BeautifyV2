<div class="button-container">
    <slot />
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .button-container {
    background: rgba($menu-base-color, 0.75);
    padding: 15px 15px;
    width: max-content;
    border-radius: 7.5px;
    display: flex;
    column-gap: 20px;
    border: 1px solid rgba(255, 255, 255, 0.15);
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.5);
    transition: 0.25s ease;
    font-family: MyCustomFont;
  }
</style>
