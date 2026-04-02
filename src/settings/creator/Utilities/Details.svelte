<script lang="ts">
    import { Platform, setIcon } from "obsidian";
    import { COLLAPSE, setNodeIcon } from "src/utils/icons";
    import WarningLabel from "./WarningLabel.svelte";
    import { WARNING } from "src/utils/icons";

    export let open = true;
    export let name: string;
    export let desc: string = "";
    export let warn: boolean = false;
    export let label: string | null = null;
    export let alwaysOpen = false;

    const details = (node: HTMLDetailsElement) => {
        if (open) node.setAttr("open", "open");
    };
    const handle = (node: HTMLElement) => {
        setIcon(node, COLLAPSE);
    };
</script>


<details
    class="calendarium-nested-settings setting-group"
    class:always-open={alwaysOpen}
    class:calendarium-details-group={!Platform.isPhone}
    bind:open
    use:details
>
    <summary
        on:keyup={(evt) => evt.preventDefault()}
    >
        <div class="setting-item setting-item-heading">
            <div class="setting-item-info">
                <div class="setting-item-name">{name}</div>
                <div class="setting-item-description">{desc}</div>
            </div>
        </div>
        <div class="right-side">
            {#if open}
                <slot name="context" class="context" />
            {/if}
            <div class="collapser">
                <div class="warning-container">
                    {#if warn}
                        <div class="x-small" use:setNodeIcon={WARNING} />
                    {/if}
                    <div class="handle" use:handle />
                </div>
                {#if warn && label}
                    <WarningLabel {label} />
                {/if}
            </div>
        </div>
    </summary>

    <div class="setting-items">
        <slot />
    </div>
</details>

<style>
    .calendarium-details-group .setting-items {
        padding-top: 0;
        padding-bottom: 0;
    }


    .always-open {
        pointer-events: none;
    }
    .calendarium-nested-settings {
        position: relative;
    }
    .right-side {
        display: flex;
        align-items: center;
        gap: 1rem;
    }
    summary::-webkit-details-marker,
    summary::marker {
        display: none !important;
    }
    .always-open .handle {
        display: none;
    }
    .collapser {
        display: flex;
        flex-flow: column;
        justify-content: flex-start;
        align-items: flex-end;
        content: "";
    }

    .handle {
        transform: rotate(0deg);
        transition: transform 0.25s;
        display: flex;
    }

    details[open] .handle {
        transform: rotate(90deg);
    }

    .calendarium-nested-settings {
        border-top: 0px;
    }
</style>