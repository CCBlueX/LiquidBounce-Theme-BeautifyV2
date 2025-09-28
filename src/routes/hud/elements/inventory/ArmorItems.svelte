<script lang="ts">
    import {listen} from "../../../../integration/ws";
    import type {ClientPlayerInventoryEvent, PlayerInventory} from "../../../../integration/events";
    import type {ItemStack} from "../../../../integration/types";
    import ItemStackView from "./ItemStackView.svelte";
    import {onMount} from "svelte";
    import {getPlayerInventory} from "../../../../integration/rest";

    let stacks: ItemStack[] = [];

    function updateStacks(inventory: PlayerInventory) {
        stacks = inventory.armor;
    }

    listen("clientPlayerInventory", (data: ClientPlayerInventoryEvent) => {
        updateStacks(data.inventory);
    });

    onMount(async () => {
        const inventory = await getPlayerInventory();
        updateStacks(inventory);
    });

    function getDurability(stack: ItemStack): number {
        if (!stack?.maxDamage) return 100;
        return Math.round(((stack.maxDamage - stack.damage) / stack.maxDamage) * 100);
    }
</script>

<div class="armor-items">
    {#each stacks as stack (stack)}
        <div class="armor-slot">
            <ItemStackView {stack}/>
            {#if stack?.maxDamage && getDurability(stack) < 100}
                <div class="durability-bar">
                </div>
            {/if}
        </div>
    {/each}
</div>

<style lang="scss">
    @use "../../../../colors.scss" as *;

  .armor-items {
    position: relative;
    display: flex;
    flex-direction: column-reverse;
    gap: 6px;
    padding: 6px;
    background: rgba($armoritems-base-color, 0.35);
    border-radius: 7.5px;
    box-shadow: 0 4px 12px rgba($armoritems-base-color, 0.5);
  }

  .armor-slot {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba($armoritems-base-color, 0.4);
    border: 1px solid rgba(150, 150, 150, 0.15);
    border-radius: 5px;
    padding: 4px;
    transition: all 0.2s ease;
  }
</style>
