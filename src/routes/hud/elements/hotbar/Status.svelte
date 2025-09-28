<script lang="ts">
    import {cubicOut} from "svelte/easing";
    import {tweened} from "svelte/motion";
    import {tick} from "svelte";

    export let max: number;
    export let value: number;
    export let color: string;
    export let alignRight: boolean;
    export let label: string | null = null;
    export let icon: string | null = null;

    const animatedValue = tweened(value, {
        duration: 400,
        easing: cubicOut
    });

    let oldValue = value;
    let flashClass = "";

    $: if (value !== oldValue) {
        if (value > oldValue) {
            flashClass = "flash-green";
        } else {
            flashClass = "flash-red";
        }
        oldValue = value;

        (async () => {
            await tick();
            setTimeout(() => flashClass = "", 400);
        })();
    }

    $: animatedValue.set(value);
    $: numericText = `${Math.floor($animatedValue)}`;
</script>

<div class="progress" class:align-right={alignRight}>
    {#if label}
        <div class="label">{label}</div>
    {/if}
    {#if icon}
        <img class="icon" src="img/hud/hotbar/icon-{icon}.svg" alt={icon}>
    {/if}

    <div
            class:align-right={alignRight}
            class="progress-bar"
            style="width: {Math.floor((value / max) * 100)}%; background-color: {color}; box-shadow: 0 0 8px {color};"
    ></div>

    <div class="value-text {flashClass}">{numericText}</div>
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .progress {
    position: relative;
    border-radius: 7.5px;
    background-color: rgba($hotbar-base-color, 0.45);
    overflow: hidden;
    font-family: MyCustomFont;

    &.align-right {
      .label {
        left: 5px;
        right: unset;
      }

      .icon {
        right: 5px;
        left: unset;
      }
    }
  }

  .progress-bar {
    border-radius: 7.5px;
    height: 20px;
    will-change: width;
    transition: width 0.35s cubic-bezier(0.22, 1, 0.36, 1), box-shadow 0.3s ease;

    &.align-right {
      margin-left: auto;
    }
  }

  .label {
    color: $hotbar-text-color;
    position: absolute;
    font-size: 14px;
    font-weight: 600;
    right: 5px;
    top: 50%;
    transform: translateY(-50%);
    text-shadow: 0 0 6px rgba(0, 0, 0, 0.5);
  }

  .icon {
    position: absolute;
    left: 5px;
    top: 50%;
    transform: translateY(-50%);
    filter: drop-shadow(0 0 4px rgba(0, 0, 0, 0.5));
  }

  .value-text {
    position: absolute;
    width: 100%;
    text-align: center;
    font-size: 13px;
    font-weight: 700;
    color: $hotbar-text-color;
    top: 50%;
    transform: translateY(-50%);
    text-shadow: 0 0 6px rgba(0, 0, 0, 0.6);
    transition: color 0.5s ease;
  }

  .flash-green {
    animation: flashGreen 0.5s ease;
  }

  .flash-red {
    animation: flashRed 0.5s ease;
  }

  @keyframes flashGreen {
    0% { color: #19c819; text-shadow: 0 0 10px #19c819; }
    100% { color: $hotbar-text-color; text-shadow: 0 0 6px rgba(0, 0, 0, 0.6); }
  }

  @keyframes flashRed {
    0% { color: #c81919; text-shadow: 0 0 10px #c81919; }
    100% { color: $hotbar-text-color; text-shadow: 0 0 6px rgba(0, 0, 0, 0.6); }
  }
</style>
