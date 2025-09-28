<script lang="ts">
    import {listen} from "../../../../integration/ws";
    import type {KeyEvent} from "../../../../integration/events";
    import type {MinecraftKeybind} from "../../../../integration/types";
    import {accentColorStore} from "../../../../theme/accentColorStore";
    import {hexToRgbString} from "../../../../integration/util";
    import {onDestroy, onMount} from "svelte";

    export let gridArea: string;
    export let key: MinecraftKeybind | undefined;

    let active = false;
    let keyElement: HTMLElement;

    let accentColor = "dodgerblue";
    const unsubscribeAccent = accentColorStore.subscribe((color: string) => {
        accentColor = hexToRgbString(color);
        if (keyElement) keyElement.style.setProperty('--accent-color', accentColor);
    });

    onMount(() => {
        if (keyElement) keyElement.style.setProperty('--accent-color', accentColor);
    });

    onDestroy(() => {
        unsubscribeAccent();
    });

    listen("key", (e: KeyEvent) => {
        if (e.key !== key?.key.translationKey) {
            return;
        }

        active = e.action === 1 || e.action === 2;
    });
</script>

<div class="key" bind:this={keyElement}
     style="grid-area: {gridArea};" class:active>
    {key?.key.localized ?? "???"}
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .key {
    height: 50px;
    background: rgba($keystrokes-base-color, 0.5);
    color: $keystrokes-text-color;
    border: 1px solid rgba(150, 150, 150, 0.15);
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 7.5px;
    font-size: 14px;
    font-weight: 700;
    transition: all 0.25s ease;
    position: relative;
    text-align: center;
    user-select: none;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.4), inset 0 0 0 0 rgba(var(--accent-color), 0.5);

    &::after {
      content: "";
      position: absolute;
      inset: 0;
      border-radius: inherit;
      box-shadow: 0 0 8px rgba(var(--accent-color), 0.35);
      opacity: 0;
      transition: opacity 0.25s ease;
    }

    &.active {
      transform: scale(0.95);
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.25), inset 0 0 15px rgba(var(--accent-color), 0.75);

      &::after {
        opacity: 1;
        box-shadow: 0 0 15px rgba(var(--accent-color), 0.75);
      }
    }
  }
</style>
