<script lang="ts">
    import { ButtonComponent, Platform } from "obsidian";
    import { onMount } from "svelte";

    import CreatorTitle from "./CreatorTitle.svelte";
    import History from "./Utilities/History.svelte";
    import General from "./Containers/general/General.svelte";
    import Dates from "./Containers/dates/Dates.svelte";
    import Celestials from "./Containers/celestials/Celestials.svelte";
    import Events from "./Containers/events/Events.svelte";
    import Eras from "./Containers/eras/EraContainer.svelte";
    import { SettingsSections, type CreatorSection } from "./creator.types";
    import { writable } from "svelte/store";
    import Sidebar from "./Containers/sidebar/Sidebar.svelte";
    import Seasonal from "./Containers/seasonal/Seasonal.svelte";
    import Locations from "./Containers/locations/Locations.svelte";
    import { getContext } from 'svelte';
    import { createEventDispatcher } from 'svelte';

    const mobile = Platform.isMobile;

    let ready = mobile;

    const store = getContext("store");
    const { valid } = store;

    onMount(() => {
        ready = true;
    });

    let selected = writable<CreatorSection>("General");

    export let color: string | null = null;
    export let top: number;

    const dispatch = createEventDispatcher<{ cancel: null, save: null }>();

    let saveButton: ButtonComponent | null = null;
    let saveNode: HTMLElement | null = null;

    const setDisabled = (btn: ButtonComponent, valid: boolean) => {
        const toolTip = valid ? '' : 'Calendar setup incomplete'

        btn.setDisabled(!valid)
        btn.setTooltip(toolTip);
    }

    const cancel = (node: HTMLDivElement) => {
        new ButtonComponent(node)
            .setButtonText("Cancel")
            .onClick(() => {
                dispatch("cancel");
            });
    };

    const save = (node: HTMLElement) => {
        saveNode = node;
        saveButton = new ButtonComponent(node)
            .setButtonText("Save")
            .setCta()
            .onClick(() => dispatch("save"));

        setDisabled(saveButton, $valid);
    };

    $: if (saveButton) {
        setDisabled(saveButton, $valid)
    }

</script>

{#if Platform.isTablet || Platform.isDesktop}
    <Sidebar {selected} sections={[...SettingsSections]} on:cancel on:save />
    <div class="creator-content-wrapper {$selected.toLowerCase()}">
        <History></History>
        <div class="tab-content-wrapper">
            <div class="vertical-tab-content calendar-editor">
                {#if $selected == "General"}
                    <General />
                {/if}
                {#if $selected == "Dates"}
                    <Dates />
                {/if}
                {#if $selected === "Eras"}
                    <Eras />
                {/if}
                {#if $selected === "Seasons & weather"}
                    <Seasonal />
                {/if}
                {#if $selected === "Locations"}
                    <Locations />
                {/if}
                {#if $selected == "Events"}
                    <Events />
                {/if}
                {#if $selected == "Celestial bodies"}
                    <Celestials />
                {/if}
            </div>
                <div class='save-buttons'>
                    <div use:cancel/>
                    <div use:save/>
                </div>

        </div>
    </div>
{:else}
    <div
        class="calendarium-creator calendarium-creator-mobile"
        style="--creator-background-color: {color}; --top: {top}px;"
    >
        {#if ready}
            <CreatorTitle />
            <div class="inherit calendarium-creator-inner">
                <div class="calendarium-creator-app">
                    <General />
                    <Dates />
                    <Celestials />
                    <Seasonal />
                    <Locations />
                    <Events />
                </div>
            </div>
        {/if}
    </div>
{/if}

<style>
    /* .calendarium-creator,
    .calendarium-creator .calendarium-creator-inner,
    .calendarium-creator .calendarium-creator-app {
        background-color: var(--creator-background-color);
    } */
    .calendarium-creator-app {
        overflow: auto;
        height: 100%;
    }
    .vertical-tab-content {
        padding: var(--size-4-8);
        padding-top: 0;
    }
    .calendarium-creator-mobile {
        padding: var(--size-4-8) !important;
    }
    .calendar-editor {
        flex-grow: 1;
    }

    .creator-content-wrapper {
        display: flex;
        flex-direction: column;
        overflow: hidden;
        flex-grow: 1;
    }

    .tab-content-wrapper {
        flex-grow: 1;
        display: grid;
        grid-template-rows: 1fr auto;
        overflow: hidden;
    }
    .save-buttons {
        display: flex;
        justify-content: flex-end;
        padding: var(--size-4-4);
        gap: .5rem;
    }
</style>
