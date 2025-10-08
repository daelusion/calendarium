<script lang="ts">
    import type { Writable } from "svelte/store";
    import CreatorTitle from "../../CreatorTitle.svelte";
    import type { CreatorSection } from "../../creator.types";
    import Sections from "./Sections.svelte";
    import { ButtonComponent } from "obsidian";
    import { createEventDispatcher } from "svelte";
    import { getContext } from 'svelte';

    export let sections: CreatorSection[];
    export let selected: Writable<CreatorSection>;

    const store = getContext("store");
    const { valid } = store;

    const dispatch = createEventDispatcher<{ cancel: null, save: null }>();
    const cancel = (node: HTMLDivElement) => {
        new ButtonComponent(node)
            .setButtonText("Cancel")
            .onClick(() => {
                dispatch("cancel");
            });
    };

    const setDisabled = (btn: ButtonComponent, valid: boolean) => {
        const toolTip = valid ? '' : 'Calendar setup incomplete'

        btn.setDisabled(!valid)
        btn.setTooltip(toolTip);
    }


    let saveButton: ButtonComponent | null = null;
    let saveNode: HTMLElement | null = null;

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

<div class="vertical-tab-header">
    <CreatorTitle />
    <div class="vertical-tab-header-group">
        <Sections {selected} {sections} />
    </div>

    <div class="bottom">
        <div class="cancel" use:cancel />
        <div use:save />
    </div>
</div>

<style scoped>
    .vertical-tab-header {
        display: flex;
        flex-flow: column nowrap;
    }
    .bottom {
        gap: .5rem;
        margin-top: auto;
        justify-content: flex-end;
        display: flex;
    }
</style>
