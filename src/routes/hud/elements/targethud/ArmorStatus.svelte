<!--<script lang="ts">-->
<!--    import {REST_BASE} from "../../../../integration/host";-->
<!--    import type {ItemStack} from "../../../../integration/types";-->
<!--    import {accentColorStore} from "../../../../theme/accentColorStore";-->
<!--    import {hexToRgbString} from "../../../../integration/util";-->
<!--    import {onDestroy, onMount} from "svelte";-->
<!--    import {getClickGuiColor} from "../../../../integration/persistent_storage";-->

<!--    export let itemStack: ItemStack;-->

<!--    let damage = Math.ceil(10 - (itemStack.damage / itemStack.maxDamage * 10));-->
<!--    $: damage = Math.ceil(10 - (itemStack.damage / itemStack.maxDamage * 10));-->

<!--    let accentColor = "dodgerblue";-->
<!--    const unsubscribeAccent = accentColorStore.subscribe((color: string) => {-->
<!--        accentColor = color;-->
<!--        document.documentElement.style.setProperty('&#45;&#45;accent-color', hexToRgbString(accentColor));-->
<!--    });-->

<!--    onMount(async () => {-->
<!--        accentColorStore.set(getClickGuiColor() ?? '#1e90ff');-->
<!--    });-->

<!--    onDestroy(() => {-->
<!--        unsubscribeAccent();-->
<!--    });-->
<!--</script>-->

<!--<div class="armor-status">-->
<!--    <img-->
<!--            class="icon"-->
<!--            src="{REST_BASE}/api/v1/client/resource/itemTexture?id={itemStack.identifier}"-->
<!--            alt={itemStack.identifier}-->
<!--    />-->
<!--    <div class="durability-bar">-->
<!--    </div>-->
<!--</div>-->

<!--<style lang="scss">-->
<!--  @use "../../../../colors.scss" as *;-->

<!--  .armor-status {-->
<!--    display: flex;-->
<!--    align-items: center;-->
<!--    column-gap: 6px;-->
<!--  }-->

<!--  .icon {-->
<!--    height: 28px;-->
<!--    width: 28px;-->
<!--    image-rendering: pixelated;-->
<!--  }-->

<!--  .durability-bar {-->
<!--    width: 45px;-->
<!--    height: 5px;-->
<!--    border-radius: 3px;-->
<!--    background: rgba(255, 255, 255, 0.1);-->
<!--    overflow: hidden;-->
<!--    border: 1px solid rgba(255, 255, 255, 0.1);-->

<!--    .fill {-->
<!--      height: 100%;-->
<!--      background: rgba(var(&#45;&#45;accent-color), 0.9);-->
<!--      transition: width 0.25s ease;-->
<!--    }-->
<!--  }-->
<!--</style>-->
