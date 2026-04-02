<script lang="ts">
    import type { Writable } from "svelte/store";
    import CreatorTitle from "../../CreatorTitle.svelte";
    import type { CreatorSection } from "../../creator.types";
    import Sections from "./Sections.svelte";
    import { getContext } from 'svelte';

    export let sections: CreatorSection[];
    export let selected: Writable<CreatorSection>;
</script>

<div class="vertical-tab-header">
    <CreatorTitle />
    <div class="vertical-tab-header-group">
        <Sections {selected} {sections} />
    </div>
</div>

<style scoped>
    .vertical-tab-header {
        display: flex;
        flex-flow: column nowrap;
    }
</style>
