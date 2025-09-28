<script lang="ts">
    import {createEventDispatcher} from "svelte";

    export let title: string;
    export let icon: string;
    export let disabled = false;

    const dispatch = createEventDispatcher();
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
<button
        class="icon-text-button"
        on:click={() => dispatch("click")}
        {disabled}
>
    <div class="icon">
        <img src="img/menu/{icon}" alt={title}>
    </div>
    <div class="title">{title}</div>
</button>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .icon-text-button {
    display: flex;
    align-items: center;
    border: none;
    outline: 1px solid rgba(255, 255, 255, 0.15);
    border-radius: 7.5px;
    overflow: hidden;
    background: linear-gradient(to left, rgba($menu-base-color, 0.45) 50%, $accent-color 50%);
    background-size: 200% 100%;
    background-position: right bottom;
    transition: background-position 0.25s ease-out, transform 0.2s ease, box-shadow 0.25s ease;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
    font-family: MyCustomFont;

    &:not([disabled]):hover {
      background-position: left bottom;
      box-shadow: 0 4px 16px rgba($accent-color, 0.4);

      .icon {
        background-color: $accent-color;
      }
    }

    &[disabled] {
      opacity: 0.5;
      cursor: not-allowed;
      background: rgba($menu-base-color, 0.3);
      box-shadow: none;

      .icon {
        background-color: rgba(120,120,120,0.4);
      }
    }
  }

  .icon {
    height: 58px;
    width: 58px;
    background-color: rgba($accent-color, 0.25);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 0.25s ease;

    img {
      width: 28px;
      height: 28px;
      filter: brightness(0) invert(1);
    }
  }

  .title {
    font-size: 18px;
    font-weight: 600;
    color: $menu-text-color;
    padding: 0 30px;
    text-shadow: 0 2px 6px rgba(0,0,0,0.5);
  }
</style>
