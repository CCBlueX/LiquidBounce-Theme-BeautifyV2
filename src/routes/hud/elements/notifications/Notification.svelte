<script lang="ts">
    import {accentColorStore} from "../../../../theme/accentColorStore";
    import {hexToRgbString} from "../../../../integration/util";
    import {onDestroy, onMount} from "svelte";
    import {getClickGuiColor} from "../../../../integration/persistent_storage";

    export let title: string;
    export let message: string;
    export let severity: string;

    let accentColor = "dodgerblue";
    let progress = 100;
    const unsubscribeAccent = accentColorStore.subscribe((color: string) => {
        accentColor = color;
        document.documentElement.style.setProperty('--accent-color', hexToRgbString(accentColor));
    });

    onMount(async () => {
        accentColorStore.set(getClickGuiColor() ?? '#1e90ff');
        setTimeout(() => {
            progress = 0;
        }, 1);
    });

    onDestroy(() => {
        unsubscribeAccent();
    });

</script>

<div class="notification">
    <div class="icon {severity.toString().toLowerCase()}"></div>
    <div class="title">{title}</div>
    <div class="message">{message}</div>
    <div class="progress-bar" style="width: {progress}%;"></div>
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .notification {
    display: grid;
    grid-template-areas:
            "a b"
            "a c";
    grid-template-columns: max-content 1fr;
    column-gap: 10px;
    background: linear-gradient(145deg, rgba($notifications-base-color, 0.8), rgba($notifications-base-color, 0.6));
    box-shadow: 0 4px 12px rgba($notifications-base-color, 0.55);
    border-radius: 5px;
    width: 250px;
    overflow: hidden;
    padding: 12px;
    margin-bottom: 10px;
    font-family: MyCustomFont;
    position: relative;
    animation: fadeInUp 0.35s ease-out;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
  }

  .icon {
    height: 35px;
    width: 35px;
    background-position: center;
    background-repeat: no-repeat;
    border-radius: 5px;
    grid-area: a;
    position: relative;
    background-image: url("/img/hud/notification/icon-toggle.svg");

    &.success {
      background-color: rgba(var(--accent-color), 0.6);
      box-shadow: 0 0 8px rgba(var(--accent-color), 0.5);
      background-image: url("/img/hud/notification/icon-success.svg");
    }

    &.error {
      background-color: rgba(var(--accent-color), 0.6);
      box-shadow: 0 0 8px rgba(var(--accent-color), 0.5);
      background-image: url("/img/hud/notification/icon-error.svg");
    }

    &.info {
      background-color: rgba(var(--accent-color), 0.6);
      box-shadow: 0 0 8px rgba(var(--accent-color), 0.5);
      background-image: url("/img/hud/notification/icon-info.svg");
    }

    &.disabled,
    &.enabled {
      &::after {
        content: "";
        position: absolute;
        height: 10px;
        width: 10px;
        border-radius: 5px;
        top: 50%;
        transform: translate(-50%, -50%);
        background: white;
        transition: all 0.25s ease-in-out;
      }
    }

    &.enabled {
      background-color: rgba(var(--accent-color), 0.6);
      box-shadow: 0 0 8px rgba(var(--accent-color), 0.5);

      &::after {
        left: 62%;
      }
    }

    &.disabled {
      background-color: rgba(var(--accent-color), 0.6);
      box-shadow: 0 0 8px rgba(var(--accent-color), 0.5);

      &::after {
        left: 38%;
      }
    }
  }

  .title {
    grid-area: b;
    font-size: 15px;
    color: #fff;
    font-weight: 700;
    letter-spacing: 0.3px;
    text-shadow: 0 0 6px rgba(var(--accent-color), 0.4);
  }

  .message {
    grid-area: c;
    font-size: 13px;
    color: #d0d6ea;
    line-height: 1.4;
  }

  .progress-bar {
    position: absolute;
    left: 0;
    bottom: 0;
    height: 4px;
    background: linear-gradient(90deg, rgba(var(--accent-color), 1), rgba(var(--accent-color), 0.5));
    box-shadow: 0 -2px 10px rgba(var(--accent-color), 0.7);
    transition: width 1.5s linear;
    z-index: 2;
    border-radius: 0 0 5px 5px;
    width: 100%;
    pointer-events: none;
  }

  @keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translateY(15px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
</style>
