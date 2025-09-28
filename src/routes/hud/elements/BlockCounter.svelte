<script lang="ts">
    import { listen } from "../../../integration/ws";
    import type { BlockCountChangeEvent, ClientPlayerDataEvent } from "../../../integration/events";
    import type { PlayerData } from "../../../integration/types";
    import { mapToColor } from "../../../util/color_utils";
    import {REST_BASE} from "../../../integration/host";
    import {accentColorStore} from "../../../theme/accentColorStore";
    import {hexToRgbString} from "../../../integration/util";
    import {onDestroy, onMount} from "svelte";
    import {getClickGuiColor} from "../../../integration/persistent_storage";

    let handBlockId: string | undefined = undefined;
    let iconUrl = $state<string | undefined>(undefined);

    let count = $state<number | undefined>(undefined);
    let speed = $state<number>(0);

    let lastX = 0, lastZ = 0;
    let playerData: PlayerData | null = null;

    const maxBlocks = 64;

    let accentColor = "dodgerblue";
    const unsubscribeAccent = accentColorStore.subscribe((color: string) => {
        accentColor = color;
        document.documentElement.style.setProperty('--accent-color', hexToRgbString(accentColor));
    });

    onMount(async () => {
        accentColorStore.set(getClickGuiColor() ?? '#1e90ff');
    });

    onDestroy(() => {
        unsubscribeAccent();
    });

    function getDynamicMaxBlocks(count: number | undefined): number {
        if (!count || count <= 64) return 64;
        if (count <= 128) return 128;
        if (count <= 192) return 192;
        if (count <= 256) return 256;
        if (count <= 320) return 320;
        if (count <= 384) return 384;
        if (count <= 448) return 448;
        if (count <= 512) return 512;
        if (count <= 576) return 576;
        return Math.ceil(count / 1000) * 1000;
    }

    let dynamicMaxBlocks = $state<number>(maxBlocks);

    listen("blockCountChange", (data: BlockCountChangeEvent) => {
        count = data.count;
        dynamicMaxBlocks = getDynamicMaxBlocks(count);
    });

    listen("clientPlayerData", (event: ClientPlayerDataEvent) => {
        if (playerData) {
            lastX = playerData.position.x;
            lastZ = playerData.position.z;
        }
        playerData = event.playerData;

        const dx = playerData.position.x - lastX;
        const dz = playerData.position.z - lastZ;
        speed = Math.sqrt(dx * dx + dz * dz) * 20;
    });

    listen("clientPlayerData", (data) => {
        handBlockId = data.playerData.mainHandStack?.identifier;
        iconUrl = handBlockId ? `${REST_BASE}/api/v1/client/resource/itemTexture?id=${handBlockId}` : undefined;
    });

</script>

{#if count !== undefined}
    <div class="block-counter">
        <div class="icon">
            {#if count > 0}
                <img src={iconUrl} alt="Block Icon" class="block-icon" />
            {/if}
        </div>
        <div class="content">
            <div class="bar-bg">
                <div class="bar-fg" style="width: {Math.min(100, (count / dynamicMaxBlocks) * 100)}%;"></div>
            </div>
            <div class="info">
                <span class="count">
                    <span class="count-number" style="color: {mapToColor(count)}">{count}</span>
                    <span class="count-label"> blocks</span>
                </span>
                <span class="speed">{speed.toFixed(2)} b/s</span>
            </div>
        </div>
    </div>
{/if}

<style lang="scss">
  @use "../../../colors.scss" as *;

  .block-counter {
    display: flex;
    align-items: center;
    background: rgba($blockcounter-base-color, 0.35);
    border: 1px solid rgba(150, 150, 150, 0.15);
    border-radius: 10px;
    padding: 10px 12px;
    min-width: 240px;
    box-shadow: 0 6px 18px rgba(0, 0, 0, 0.35), 0 2px 6px rgba(0, 0, 0, 0.25);
    transition: transform 0.25s ease, box-shadow 0.25s ease;

    .icon {
      margin-right: 14px;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 40px;
      height: 40px;
      border-radius: 8px;
      background: rgba(var(--accent-color), 0.18);
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.4), inset 0 0 6px rgba(var(--accent-color), 0.35);

      .block-icon {
        width: 32px;
        height: 32px;
        animation: fadeIn 0.25s ease;
      }
    }

    .content {
      flex: 1;
      display: flex;
      flex-direction: column;

      .bar-bg {
        width: 100%;
        height: 7px;
        background: rgba(var(--accent-color), 0.15);
        border: 1px solid rgba(150, 150, 150, 0.15);
        border-radius: 4px;
        margin-bottom: 8px;
        overflow: hidden;
        box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.35);

        .bar-fg {
          height: 100%;
          background: linear-gradient(90deg, rgba(var(--accent-color), 0.65), rgba(var(--accent-color), 1));
          border-radius: 4px;
          transition: width 0.25s ease-in-out;
          box-shadow: 0 0 8px rgba(var(--accent-color), 0.6), 0 0 16px rgba(var(--accent-color), 0.4);
        }
      }

      .info {
        display: flex;
        justify-content: space-between;
        align-items: baseline;

        .count {
          font-weight: 700;
          font-size: 16px;
          color: white;

          .count-number {
            font-size: 16px;
            text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
          }

          .count-label {
            margin-left: 2px;
            color: white;
            font-size: 16px;
            font-weight: 600;
          }
        }

        .speed {
          font-size: 14px;
          color: rgba(150, 150, 150, 1);
          text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
        }
      }
    }
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: scale(0.85); }
    to { opacity: 1; transform: scale(1); }
  }
</style>
