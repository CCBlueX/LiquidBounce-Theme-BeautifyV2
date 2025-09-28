<script lang="ts">
    import Header from "./header/Header.svelte";
    import {fly} from "svelte/transition";
    import {onMount} from "svelte";

    const transitionDuration = 700; // TODO: suboptimal

    let ready = false;

    onMount(() => {
        setTimeout(() => {
            ready = true;
        }, transitionDuration);
    });
</script>

<div class="menu">
    {#if ready}
        <div transition:fly|global={{duration: 700, y: -100}}>
            <Header/>
        </div>

        <div class="menu-wrapper">
            <slot/>
        </div>
    {/if}
</div>

<style lang="scss">
  .menu {
    padding: 50px;
    display: flex;
    flex-direction: column;
    height: 100vh;
    //background-image: url('https://thespawnchunks.com/wp-content/uploads/2024/10/2024-10-07_08.46.55.jpeg');
  }

  .menu-wrapper {
    flex: 1;
    display: flex;
    flex-direction: column;
    will-change: transform;
  }

  @media screen and (max-width: 1366px) {
    .menu {
      zoom: 0.8;
      height: 125vh;
    }
  }

  @media screen and (max-width: 1200px) {
    .menu {
      zoom: 0.5;
      height: 200vh;
    }
  }

  @media screen and (max-height: 1100px) {
    .menu {
      zoom: 0.8;
      height: 125vh;
    }
  }

  @media screen and (max-height: 700px) {
    .menu {
      zoom: 0.5;
      height: 200vh;
    }
  }

  @media screen and (max-height: 540px) {
    .menu {
      zoom: 0.4;
      height: 250vh;
    }
  }
</style>
