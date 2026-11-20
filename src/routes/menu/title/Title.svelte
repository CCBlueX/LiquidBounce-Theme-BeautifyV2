<script lang="ts">
    import MainButton from "./buttons/MainButton.svelte";
    import ConfettiBackground from "./ConfettiBackground.svelte";
    import ButtonContainer from "../common/buttons/ButtonContainer.svelte";
    import IconTextButton from "../common/buttons/IconTextButton.svelte";
    import IconButton from "../common/buttons/IconButton.svelte";
    import {
        openLink,
        exitClient,
        getClientUpdate,
        openScreen,
        toggleBackgroundShaderEnabled,
        toggleBasicMode
    } from "../../../integration/rest";
    import {fly} from "svelte/transition";
    import {onMount} from "svelte";
    import {notification} from "../common/header/notification_store";
    import {isAnniversary} from "../../../util/utils";
    import {escapeLayer} from "../../../integration/escape";
    import {prefetchServers} from "../multiplayer/server_store";

    let regularButtonsShown = true;
    let clientButtonsShown = false;

    onMount(() => {
        setTimeout(async () => {
            const clientUpdate = await getClientUpdate();

            if (clientUpdate.update) {
                notification.set({
                    title: `LiquidBounce ${clientUpdate.update.clientVersion} has been released!`,
                    message: `Download it from liquidbounce.net!`,
                    error: false,
                    delay: 99999999
                });
            }
        }, 2000);
    });

    function toggleButtons() {
        if (clientButtonsShown) {
            clientButtonsShown = false;
            regularButtonsShown = true;
        } else {
            regularButtonsShown = false;
            clientButtonsShown = true;
        }
    }

    const layer = escapeLayer(toggleButtons);
    $: layer.setOpen(clientButtonsShown);
</script>

<div class="title-screen">
    {#if isAnniversary()}
        <ConfettiBackground/>
    {/if}

    <div class="content">
        <img class="logo" transition:fly|global={{duration: 500, y: -100}} src="img/lb-logo.svg" alt="logo">
        <div class="main-buttons">
            {#if regularButtonsShown}
                <MainButton title="Singleplayer" icon="singleplayer" index={0}
                            on:click={() => openScreen("singleplayer")}/>

                <MainButton title="Multiplayer" icon="multiplayer" let:parentHovered
                            on:mouseenter={prefetchServers}
                            on:click={() => openScreen("multiplayer")} index={1}>
                </MainButton>
                <MainButton title="LiquidBounce" icon="liquidbounce" on:click={toggleButtons} index={2}/>
                <MainButton title="Options" icon="options" on:click={() => openScreen("options")} index={3}/>
            {:else if clientButtonsShown}
                <MainButton title="Proxy Manager" icon="proxymanager" on:click={() => openScreen("proxymanager")}
                            index={0}/>
                <MainButton title="Click GUI" icon="clickgui" on:click={() => openScreen("clickgui")} index={1}/>
                <!-- <MainButton title="Scripts" icon="scripts" index={2}/> -->
                <MainButton title="Back" icon="back-large" on:click={toggleButtons} index={2}/>
            {/if}
        </div>

        <div class="additional-buttons" in:fly|global={{duration: 180, y: -20}}>
            <ButtonContainer>
                <IconTextButton icon="icon-exit.svg" title="Exit" on:click={exitClient}/>
                <IconTextButton icon="icon-eye.svg" title="Basic Mode" on:click={toggleBasicMode}/>
            </ButtonContainer>
        </div>

        <div class="social-buttons" in:fly|global={{duration: 180, y: -20}}>
            <ButtonContainer>
                <IconButton title="Forum" icon="nodebb" on:click={() => openLink("MAINTAINER_FORUM")}/>
                <IconButton title="GitHub" icon="github" on:click={() => openLink("MAINTAINER_GITHUB")}/>
                <IconButton title="Discord" icon="discord" on:click={() => openLink("MAINTAINER_DISCORD")}/>
                <IconButton title="Twitter" icon="twitter" on:click={() => openLink("MAINTAINER_TWITTER")}/>
                <IconButton title="YouTube" icon="youtube" on:click={() => openLink("MAINTAINER_YOUTUBE")}/>
                <IconTextButton title="liquidbounce.net" icon="icon-liquidbounce.net.svg"
                                on:click={() => openLink("CLIENT_WEBSITE")}/>
            </ButtonContainer>
        </div>
    </div>
</div>

<style>
    .title-screen {
        position: relative;
        isolation: isolate;
        display: flex;
        flex: 1;
        flex-direction: column;
    }

    .content {
        flex: 1;
        display: grid;
        grid-template-areas:
            "a ."
            "b c";
        grid-template-rows: 1fr max-content;
        grid-template-columns: 1fr max-content;
    }

    .main-buttons {
        display: flex;
        position: fixed;
        top: 50%;
        left: 50%;
        translate: -50% -50%;
        flex-direction: column;
        row-gap: 15px;
        grid-area: a;
    }

    .additional-buttons {
        grid-area: b;
    }

    .social-buttons {
        grid-area: c;
    }

    .logo {
        position: fixed;
        /* Offset from the centered button stack, so the logo stays clear of it in short windows */
        top: calc(50% - 285px);
        left: 50%;
        translate: -50% -50%;
    }
</style>
