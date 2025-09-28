<script lang="ts">
    import {type ComponentType, createEventDispatcher} from "svelte";

    let availableTabsElement: HTMLElement | undefined;

    export let tabs: {
        title: string,
        icon: string,
        component: ComponentType,
    }[];
    export let activeTab = 0;

    const dispatch = createEventDispatcher<{
        changeTab: { activeTab: number }
    }>();

    function setActiveTab(i: number) {
        activeTab = i;
        dispatch("changeTab", {activeTab});
    }
</script>

<div class="tabs">
    <div class="available-tabs" bind:this={availableTabsElement}>
        {#each tabs as {title, icon}, index}
            <button class="tab-button" class:active={tabs[activeTab].title === title}
                    on:click={() => setActiveTab(index)}>
                <img class="icon" src="img/menu/altmanager/{icon}" alt={title}>
                <span>{title}</span>
            </button>
        {/each}
    </div>

    <div style="width: {availableTabsElement?.clientWidth}px">
        <svelte:component this={tabs[activeTab].component}/>
    </div>
</div>

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .available-tabs {
    display: flex;
    column-gap: 15px;
    margin-bottom: 40px;
  }

  .tab-button {
    font-family: MyCustomFont;
    background: linear-gradient(to top, rgba($menu-base-color, .36), rgba($menu-base-color, .5));
    color: $menu-text-color;
    padding: 10px;
    border: solid 2px transparent;
    border-radius: 7.5px;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    row-gap: 10px;
    cursor: pointer;
    transition: border-color .25s ease, background-color .25s ease, transform .2s ease, box-shadow .25s ease;

    .icon-wrapper {
      background-color: rgba($accent-color, .15);
      border-radius: 50%;
      padding: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: background-color .25s ease;
    }

    .icon {
      height: 30px;
      filter: drop-shadow(0 0 3px rgba($accent-color, .4));
      transition: transform .25s ease;
    }

    .tab-title {
      font-size: 16px;
      font-weight: 500;
      letter-spacing: .5px;
      transition: color .25s ease;
    }

    &:hover {
      border-color: rgba($accent-color, .6);
      background-color: rgba($menu-base-color, .55);
      box-shadow: 0 4px 12px rgba($accent-color, 0.25);

      .icon {
        transform: scale(1.1);
      }

      .icon-wrapper {
        background-color: rgba($accent-color, .25);
      }
    }

    &.active {
      border-color: $accent-color;
      background-color: rgba($menu-base-color, .65);
      box-shadow: 0 0 15px rgba($accent-color, .4);

      .tab-title {
        color: $accent-color;
        text-shadow: 0 0 8px rgba($accent-color, .8);
      }

      .icon {
        transform: scale(1.15);
        filter: drop-shadow(0 0 6px rgba($accent-color, .9));
      }
    }
  }

  .tab-content {
    animation: fadeIn .3s ease;
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translateY(10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
</style>
