<script lang="ts">
    import { accentColorStore } from "../../../../theme/accentColorStore";
    import { hexToRgbString } from "../../../../integration/util";
    import { onDestroy, onMount } from "svelte";
    import { getClickGuiColor } from "../../../../integration/persistent_storage";

    export let maxHealth: number;
    export let health: number;

    let width = Math.ceil((health / maxHealth) * 100);
    let prevWidth = width;

    $: {
        let newWidth = Math.ceil((health / maxHealth) * 100);
        if (newWidth < prevWidth) {
            setTimeout(() => (prevWidth = newWidth), 450);
        } else {
            prevWidth = newWidth;
        }
        width = newWidth;
    }

    let accentColor = "dodgerblue";
    const unsubscribeAccent = accentColorStore.subscribe((color: string) => {
        accentColor = color;
        document.documentElement.style.setProperty(
            "--accent-color",
            hexToRgbString(accentColor)
        );
    });

    onMount(async () => {
        accentColorStore.set(getClickGuiColor() ?? "#1e90ff");
    });

    onDestroy(() => {
        unsubscribeAccent();
    });
</script>

<div class="health-progress">
    <div class="delayed-thumb" style="width: {prevWidth}%;"></div>
    <div class="thumb" style="width: {width}%;"></div>
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .health-progress {
    position: relative;
    width: 100%;
    height: 8px;
    border-radius: 8px;
    background: rgba(var(--accent-color), 0.15);
    box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.35);
    border: 1px solid rgba(150, 150, 150, 0.15);
    overflow: hidden;
  }

  .delayed-thumb {
    position: absolute;
    height: 100%;
    background: rgba(var(--accent-color), 0.35);
    transition: width 0.25s ease-in-out;
    border-radius: 8px;
  }

  .thumb {
    position: absolute;
    height: 100%;
    background: linear-gradient(90deg, rgba(var(--accent-color), 0.65), rgba(var(--accent-color), 1));
    border-radius: 8px;
    transition: width 0.25s ease-in-out;
    box-shadow: 0 0 8px rgba(var(--accent-color), 0.6), 0 0 16px rgba(var(--accent-color), 0.4);
  }
</style>
