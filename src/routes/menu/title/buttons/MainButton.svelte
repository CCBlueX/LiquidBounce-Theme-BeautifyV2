<script lang="ts">
    import {fade, fly, scale} from "svelte/transition";
    import {createEventDispatcher} from "svelte";
    import {backIn, backOut, cubicIn, cubicOut} from "svelte/easing";

    export let title: string;
    export let icon: string;
    let isMenuActive = false;
    export let index: number;

    let hovered = false;

    const dispatch = createEventDispatcher();
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<!-- svelte-ignore a11y-click-events-have-key-events -->
<div class="main-button" on:mouseenter={() => hovered = true} on:mouseleave={() => hovered = false} on:click={() => hovered = false}
     on:click={() => dispatch("click")} out:fly|global={{duration: 550, y: -100, delay: (3 - index) * 125, easing: cubicOut}}
     in:fly|global={{duration: 550, y: -100, delay: index * 125, easing: cubicIn}}>

    <div class="icon">
        {#if !hovered}
            <img transition:fade={{duration: 250}} src="img/menu/icon-{icon}.svg" alt={icon}>
        {:else}
            <img transition:fade={{duration: 250}} src="img/menu/icon-{icon}-hover.svg" alt={icon}>
        {/if}
    </div>

    <div class="title">{title}</div>

    <div class="wrapped-content">
        <slot parentHovered={hovered}/>
    </div>
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .main-button {
    background: rgba($menu-base-color, 0.75);
    border: 1px solid rgba(255, 255, 255, 0.15);
    width: 300px;
    height: 85px;
    padding: 10px;
    display: grid;
    grid-template-columns: max-content 1fr max-content;
    align-items: center;
    cursor: pointer;
    border-radius: 7.5px;
    box-shadow: 0 6px 16px rgba(0, 0, 0, 0.5);
    column-gap: 15px;
    transition: transform 0.25s ease, box-shadow 0.25s ease;

    &:hover {
      box-shadow: 0 6px 16px rgba($accent-color, 0.5);
      border: 1px solid rgba($accent-color, 0.75);

      .icon {
        background: white;
        box-shadow: 0 0 12px rgba($accent-color, 0.75);
      }

      .title {
        color: white;
        text-shadow: 0 0 12px rgba($accent-color, 0.75);
      }
    }
  }

  .icon {
    background: $accent-color;
    width: 65px;
    height: 65px;
    border-radius: 50%;
    transition: background-color 0.25s ease, box-shadow 0.25s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    box-shadow: 0 2px 12px rgba(0,0,0,0.6);

    img {
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
    }
  }

  .title {
    font-size: 20px;
    color: $menu-text-color;
    font-weight: 700;
    text-shadow: 0 2px 8px rgba(0, 0, 0, 0.5);
    font-family: MyCustomFont;
    transition: color 0.25s ease, text-shadow 0.25s ease;
  }

  .wrapped-content {
    opacity: 0.85;
    transition: opacity 0.25s ease;
  }
</style>
