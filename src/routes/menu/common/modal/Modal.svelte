<script lang="ts">
    import {fade, fly, scale} from "svelte/transition";
    import {createEventDispatcher} from "svelte";

    export let title: string;
    export let visible: boolean;

    const dispatch = createEventDispatcher();

    function handleClick() {
        dispatch("close");
        visible = false;
    }
</script>

{#if visible}
    <div class="modal-wrapper" transition:fade|global={{duration: 200}}>
        <div class="modal"
             in:fly|global={{duration: 300, y: -80}}
             out:fly|global={{duration: 250, y: -80}}>
            <button class="button-modal-close" on:click={handleClick}>
                <img src="img/menu/icon-close.svg" alt="close">
            </button>

            <div class="title">{title}</div>

            <div class="content">
                <slot />
            </div>
        </div>
    </div>
{/if}

<style lang="scss">
  @use "../../../../colors.scss" as *;

  .modal-wrapper {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    backdrop-filter: blur(10px);
    background-color: rgba($menu-base-color, 0.5);
    z-index: 99999;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .modal {
    background: linear-gradient(145deg, rgba($menu-base-color, 0.85), rgba($menu-base-color, 0.65));
    border: 2px solid rgba($accent-color, 0.4);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.65), 0 0 15px rgba($accent-color, 0.3);
    min-width: 500px;
    max-width: 90vw;
    max-height: 85vh;
    overflow-y: auto;
    border-radius: 7.5px;
    padding: 40px;
    display: flex;
    flex-direction: column;
    position: relative;
    font-family: MyCustomFont;
  }

  .title {
    color: $menu-text-color;
    font-size: 32px;
    font-weight: 600;
    text-align: center;
    margin-bottom: 50px;
    position: relative;
    text-shadow: 0 2px 12px rgba(0, 0, 0, 0.6);

    &::after {
      content: "";
      position: absolute;
      display: block;
      height: 4px;
      width: 60%;
      background: $accent-color;
      bottom: -18px;
      left: 50%;
      transform: translateX(-50%);
      border-radius: 3px;
      box-shadow: 0 0 12px rgba($accent-color, 0.7);
    }
  }

  .content {
    display: flex;
    flex-direction: column;
    row-gap: 30px;
    color: $menu-text-color;
  }

  .button-modal-close {
    height: 40px;
    width: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: transparent;
    border: 2px solid rgba($menu-text-color, 0.6);
    border-radius: 50%;
    cursor: pointer;
    position: absolute;
    top: 20px;
    right: 20px;
    transition: 0.25s ease;

    img {
      width: 20px;
      height: 20px;
    }

    &:hover {
      background-color: rgba(red, 0.2);
      border-color: red;
      transform: rotate(90deg) scale(1.1);
    }
  }

  @media screen and (max-width: 768px) {
    .modal {
      min-width: unset;
      width: 90%;
      padding: 25px;
    }

    .title {
      font-size: 24px;
    }
  }
</style>
