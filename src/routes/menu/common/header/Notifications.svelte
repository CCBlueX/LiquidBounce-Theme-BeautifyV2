<script lang="ts">
    import {fly} from "svelte/transition";
    import {notification, type TNotification} from "./notification_store";
    import {onMount, tick} from "svelte";

    let current: TNotification | null = null;
    let pending: TNotification | null = null;
    let visible = false;
    let leaving = false;
    let mounted = false;
    let dismissTimeout: ReturnType<typeof setTimeout> | undefined;

    function showPending() {
        if (!mounted) {
            return;
        }

        current = pending;
        pending = null;
        leaving = false;
        visible = current !== null;
    }

    async function hideCurrent() {
        clearTimeout(dismissTimeout);
        if (leaving || !current) {
            return;
        }

        leaving = true;
        await tick();
        if (mounted) {
            visible = false;
        }
    }

    function scheduleDismiss() {
        if (!mounted || leaving || !visible || !current) {
            return;
        }

        dismissTimeout = setTimeout(hideCurrent, (current.delay ?? 3) * 1000);
    }

    onMount(() => {
        mounted = true;
        const unsubscribe = notification.subscribe((value) => {
            if (!value) {
                return;
            }

            pending = {...value};
            notification.set(null);

            if (current) {
                hideCurrent();
            } else {
                showPending();
            }
        });

        return () => {
            mounted = false;
            unsubscribe();
            clearTimeout(dismissTimeout);
        };
    });
</script>

<div class="notifications">
    {#if visible && current}
        <div class="notification"
             transition:fly|global={{duration: 180, y: -20}}
             on:introend={scheduleDismiss}
             on:outroend={showPending}>
            <div class="icon" class:error={current.error}>
                <img src="img/hud/notification/icon-info.svg" alt="info">
            </div>
            <div class="title">{current.title}</div>
            <div class="message">{current.message}</div>
        </div>
    {/if}
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;


  .notifications {
    display: flex;
    flex-direction: column;
    gap: 12px;
    position: fixed;
    top: 5%;
    left: 50%;
    transform: translateX(-50%);
    z-index: 2000;
    font-family: MyCustomFont;
  }

  .notification {
    display: grid;
    grid-template-areas:
      "a b"
      "a c";
    grid-template-columns: max-content 1fr;
    align-items: center;
    min-width: 360px;
    padding: 12px 12px;
    border-radius: 7.5px;
    background: rgba($menu-base-color, 0.75);
    border: 1px solid rgba(255, 255, 255, 0.15);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.5);
    position: relative;
    overflow: hidden;

    &.error::before {
      background: $menu-error-color;
    }

    .icon {
      grid-area: a;
      height: 60px;
      width: 60px;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-right: 14px;
      background: rgba($accent-color, 0.25);
      border: 1px solid rgba($accent-color, 0.25);
      box-shadow: 0 0 10px rgba($accent-color, 0.5);

      transition: all 0.25s ease;

      img {
        width: 28px;
        height: 28px;
      }

      &.error {
        background: rgba($menu-error-color, 0.15);
        box-shadow: 0 0 12px rgba($menu-error-color, 0.6);

        img {
        }
      }
    }

    .title {
      grid-area: b;
      font-weight: 700;
      font-size: 18px;
      color: $menu-text-color;
      text-shadow: 0 0 6px rgba(0,0,0,0.5);
    }

    .message {
      grid-area: c;
      font-size: 15px;
      font-weight: 500;
      margin-top: 2px;
      color: $menu-text-dimmed-color;
      text-shadow: 0 0 4px rgba(0,0,0,0.4);
    }
  }
</style>
