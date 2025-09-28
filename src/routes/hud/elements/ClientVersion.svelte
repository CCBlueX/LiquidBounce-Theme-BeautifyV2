<!--
  - This file is part of LiquidBounce (https://github.com/CCBlueX/LiquidBounce)
  -
  - Copyright (c) 2015 - 2025 CCBlueX
  -
  - LiquidBounce is free software: you can redistribute it and/or modify
  - it under the terms of the GNU General Public License as published by
  - the Free Software Foundation, either version 3 of the License, or
  - (at your option) any later version.
  -
  - LiquidBounce is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
  - GNU General Public License for more details.
  -
  - You should have received a copy of the GNU General Public License
  - along with LiquidBounce. If not, see <https://www.gnu.org/licenses/>.
  -->

<script lang="ts">
    import { onMount } from "svelte";
    import { getClientUpdate } from "../../../integration/rest";
    import type { ClientUpdate } from "../../../integration/types";

    let clientUpdate: ClientUpdate | null = null;

    onMount(async () => {
        clientUpdate = await getClientUpdate();
    });
</script>

{#if clientUpdate && clientUpdate.update && clientUpdate.update.commitId}
    <div class="client-version">
        Build - git-{clientUpdate.update.commitId.slice(0, 7)}
        {#if clientUpdate.update.clientVersion}
            | V{clientUpdate.update.clientVersion}
        {/if}
    </div>
{/if}

<style lang="scss">
.client-version {
    position: relative;
    font-family: MyCustomFont;
    font-size: 14px;
    color: #fff;
    opacity: 0.8;
    pointer-events: none;
}
</style>
