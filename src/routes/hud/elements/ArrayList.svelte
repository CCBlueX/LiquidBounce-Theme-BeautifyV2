<script lang="ts">
    import {onMount, tick, onDestroy} from "svelte";
    import type {Module} from "../../../integration/types";
    import {getModules} from "../../../integration/rest";
    import {listen} from "../../../integration/ws";
    import {getTextWidth} from "../../../integration/text_measurement";
    import {flip} from "svelte/animate";
    import {fly, scale} from "svelte/transition";
    import {convertToSpacedString, spaceSeperatedNames} from "../../../theme/theme_config";
    import {accentColorStore} from '../../../theme/accentColorStore';
    import {hexToRgbString} from "../../../integration/util";

    let enabledModules: Module[] = [];
    let arraylist: HTMLElement;

    let accentColor = "dodgerblue";
    const unsubscribeAccent = accentColorStore.subscribe((color: string) => {
        accentColor = hexToRgbString(color);
        if (arraylist) arraylist.style.setProperty('--accent-color', accentColor);
    });

    onMount(() => {
        if (arraylist) arraylist.style.setProperty('--accent-color', accentColor);
    });

    onDestroy(() => {
        unsubscribeAccent();
    });

    async function updateEnabledModules() {
        const modules = await getModules();
        const visibleModules = modules.filter(m => m.enabled && !m.hidden);

        const modulesWithWidths = visibleModules.map(module => {
                let formattedName = $spaceSeperatedNames ? convertToSpacedString(module.name) : module.name;
                let fullName = module.tag == null ? formattedName : formattedName + " " + module.tag;

                return {
                    ...module,
                    width: getTextWidth(fullName, "600 15px MyCustomFont")
                };
            }
        );

        modulesWithWidths.sort((a, b) => b.width - a.width);

        enabledModules = modulesWithWidths;
        await tick();
    }

    spaceSeperatedNames.subscribe(async () => {
        await updateEnabledModules();
    });

    onMount(async () => {
        await updateEnabledModules();
    });

    listen("moduleToggle", async () => {
        await updateEnabledModules();
    });

    listen("refreshArrayList", async () => {
        await updateEnabledModules();
    });
</script>

<div class="arraylist" bind:this={arraylist}>
    {#each enabledModules as {name, tag} (name)}
        <div class="module" animate:flip={{ duration: 200 }} transition:scale={{ duration: 200, start: 0.8 }}>
            {$spaceSeperatedNames ? convertToSpacedString(name) : name}
            {#if tag}
                <span class="tag"> {tag}</span>
            {/if}
        </div>
    {/each}
</div>

<style lang="scss">
  @use "../../../colors.scss" as *;

  .arraylist {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
  }

  .module {
    position: relative;
    margin-left: auto;
    padding: 3px 6px;
    font-size: 15px;
    font-weight: 600;
    font-family: MyCustomFont;
    border-radius: 4px 0 0 4px;
    background: rgba($arraylist-base-color, 0.45);
    color: rgba(var(--accent-color), 1);
    text-shadow: 0 0 8px rgba(var(--accent-color), 0.75);
    width: max-content;

    &::after {
      content: "";
      position: absolute;
      right: -1px;
      top: 5%;
      bottom: 5%;
      width: 2px;
      border-radius: 2px;
      background: rgba(var(--accent-color), 1);
      box-shadow: 0 0 6px rgba(var(--accent-color), 0.75);
    }
  }

  .tag {
    color: $arraylist-tag-color;
    text-shadow: none;
  }

  @font-face {
    font-family: "MyCustomFont";
    src: url("/fonts/Montserrat-VariableFont_wght.ttf");
    font-weight: normal;
    font-style: normal;
  }
</style>
