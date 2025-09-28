<script lang="ts">
    import {createEventDispatcher} from "svelte";

    let value = "";

    const dispatch = createEventDispatcher<{
        search: { query: string }
    }>();
</script>

<input class="search" type="text" placeholder="Search..." bind:value={value}
       on:input={() => dispatch("search", {query: value})} spellcheck="false">

<style lang="scss">
  @use "../../../colors.scss" as *;

  .search {
    background-color: rgba($menu-base-color, 0.5);
    border: 1px solid rgba(255, 255, 255, 0.15);
    color: $menu-text-color;
    font-family: MyCustomFont;
    font-size: 20px;
    border-radius: 7.5px;
    background-image: url("/img/menu/icon-search.svg");
    background-repeat: no-repeat;
    background-position: 20px center;
    padding: 20px 20px 20px 60px;
    flex: 1;
  }
</style>
