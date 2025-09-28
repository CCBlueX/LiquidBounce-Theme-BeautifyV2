<script lang="ts">
    export let icon: string;
    export let title: string;
    export let type = "text";
    export let value = "";
    export let maxLength: number | null = null;
    export let pattern: string | null = null;
</script>

<div class="icon-text-input">
    <div class="icon">
        <img src="img/menu/icon-{icon}.svg" alt={icon}>
    </div>
    {#if type === "text"}
        <input {pattern} maxlength={maxLength} class="input" spellcheck="false" type="text" placeholder={title} bind:value={value} autocomplete="off">
    {:else if type === "password"}
        <input {pattern} maxlength={maxLength} class="input" type="password" placeholder={title} bind:value={value} autocomplete="off">
    {/if}
    <div class="button-container">
        <slot />
    </div>
</div>

<style lang="scss">
  @use "sass:color";
  @use "../../../../colors.scss" as *;

  .icon-text-input {
    display: grid;
    grid-template-columns: max-content 1fr max-content;
    align-items: center;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 0 6px rgba($accent-color, 0.25);
    transition: box-shadow 0.25s ease;

    &:focus-within {
      box-shadow: 0 0 12px rgba($accent-color, 0.7);
    }
  }

  .icon {
    height: 64px;
    width: 64px;
    background: linear-gradient(145deg, color.adjust($accent-color, $lightness: 10%), color.adjust($accent-color, $lightness: -5%));
    display: flex;
    align-items: center;
    justify-content: center;
    border-right: solid 2px rgba($menu-base-color, 0.6);

    img {
      height: 28px;
      width: 28px;
      filter: drop-shadow(0 0 4px rgba($menu-text-color, 0.6));
    }
  }

  .input {
    color: $menu-text-color;
    font-family: MyCustomFont;
    font-size: 18px;
    background-color: rgba($menu-base-color, .36);
    border: none;
    padding: 0 15px;
    height: 64px;
    width: 100%;
    outline: none;
    transition: background-color 0.25s ease, border 0.25s ease;

    &::placeholder {
      color: rgba($menu-text-color, 0.5);
    }

    &:focus {
      background-color: rgba($menu-base-color, 0.55);
    }

    &:invalid {
      border: solid 2px $menu-error-color;
      background-color: rgba($menu-error-color, 0.1);
    }
  }

  .button-container {
    display: flex;
    align-items: center;
    padding-right: 12px;

    > * {
      margin-left: 8px;
    }
  }
</style>
