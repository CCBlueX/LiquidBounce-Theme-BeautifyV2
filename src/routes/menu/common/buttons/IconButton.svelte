<script lang="ts">
    import ToolTip from "../ToolTip.svelte";
    import {createEventDispatcher} from "svelte";

    export let title: string;
    export let icon: string;

    const dispatch = createEventDispatcher();
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="icon-button" on:click={() => dispatch("click")}>
    <ToolTip text={title}/>

    <div class="icon">
        <img src="img/menu/icon-{icon}.svg" alt={icon}>
    </div>
</div>

<style lang="scss">
  .icon-button {
    position: relative;
  }

  .icon {
    height: 55px;
    width: 55px;
    border-radius: 25%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    border: 1px solid rgba(255, 255, 255, 0.15);
    transition: 0.25s ease;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);

    img {
      width: 28px;
      height: 28px;
    }

    &:hover {
      transform: translateY(-3px);
    }
  }
</style>
