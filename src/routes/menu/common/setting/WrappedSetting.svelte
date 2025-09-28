<script lang="ts">
    import type {BooleanSetting as TBooleanSetting, ModuleSetting,} from "../../../../integration/types";
    import {fade} from "svelte/transition";
    import SwitchSetting from "./SwitchSetting.svelte";
    import GenericSetting from "../../../clickgui/setting/common/GenericSetting.svelte";
    import {quintOut} from "svelte/easing";
    import {convertToSpacedString} from "../../../../theme/theme_config";
    import {accentColorStore} from "../../../../theme/accentColorStore";
    import {hexToRgbString} from "../../../../integration/util";
    import {onDestroy, onMount} from "svelte";
    import {getClickGuiColor} from "../../../../integration/persistent_storage";

    interface Props {
        value: NesterSetting,
        path: string
    }

    interface NesterSetting {
        name: string;
        valueType: string;
        value: ModuleSetting[];
    }

    const {value = $bindable(), path}: Props = $props();

    const enabledSetting = value.value[0] as TBooleanSetting;

    let expanded = $state(false);
    let wrappedSettingElement: HTMLElement;
    let headerElement: HTMLElement;

    function handleWrapperClick(e: MouseEvent) {
        if (!expanded) {
            expanded = true;
        } else {
            expanded = !headerElement.contains(e.target as Node);
        }
    }

    function handleWindowClick(e: MouseEvent) {
        if (!wrappedSettingElement) return;

        const node = e.target as HTMLElement;

        if (!wrappedSettingElement.contains(node)
            && !node.classList.contains("option")) { // Don't close when a select option is pressed
            expanded = false;
        }
    }

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
</script>

<svelte:window on:click={handleWindowClick}/>

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<div class="wrapped-setting" class:expanded class:has-nested-settings={value.value.length > 0}
     onclick={handleWrapperClick} bind:this={wrappedSettingElement}>
    <div class="header" bind:this={headerElement}>
        {#if value.valueType === "TOGGLEABLE"}
            <SwitchSetting title={convertToSpacedString(value.name)} bind:value={enabledSetting.value}/>
        {:else if value.valueType === "CONFIGURABLE"}
            <span class="configurable-title">{convertToSpacedString(value.name)}</span>
        {:else }
            Unsupported value type {value.valueType}
        {/if}
        {#if value.value.length > 0}
            <img src="img/menu/icon-select-arrow.svg" alt="expand">
        {/if}
    </div>

    {#if expanded && value.value.length > 0}
        <div class="nested-settings" transition:fade|global={{ duration: 200, easing: quintOut }}>
            {#each value.value as setting, i (setting.name)}
                <GenericSetting {path} bind:setting={value.value[i]} moduleName={value.name} on:change/>
            {/each}
        </div>
    {/if}
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .configurable-title {
    color: $menu-text-color;
    font-size: 20px;
    font-weight: 600;
  }

  .wrapped-setting {
    position: relative;
    min-width: 300px;
    font-family: MyCustomFont;

    &.expanded {
      .header {
        border-radius: 0 0 7.5px 7.5px;
      }
    }

    &.has-nested-settings {
      cursor: pointer;

      .header {
        background-color: rgba($menu-base-color, .75);
        border: 1px solid rgba(255, 255, 255, 0.15);
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
        padding: 15px;
        display: flex;
        column-gap: 15px;
        align-items: center;
        justify-content: space-between;
        border-radius: 7.5px;
        transition: ease border-radius .2s;
      }
    }
  }

  .nested-settings {
    position: absolute;
    z-index: 1000;
    border-radius: 7.5px 7.5px 7.5px 7.5px;
    margin-top: 3px;
    background-color: rgba($menu-base-color, 0.9);
    border: 1px solid rgba(255, 255, 255, 0.15);
    padding: 10px 13px;
    zoom: 1.5;
    width: 100%;
  }
</style>
