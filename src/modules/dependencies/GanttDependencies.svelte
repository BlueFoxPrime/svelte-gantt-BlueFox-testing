<script lang="ts">
    import { getContext } from 'svelte';
    import type { GanttContextDimensions } from '../../gantt'
    import { taskStore } from '../../core/store';
    import Dependency from './Dependency.svelte';

    const { visibleHeight } : GanttContextDimensions = getContext('dimensions');

    export let paddingTop;
    export let dependencies = [];

    let visibleDependencies = [];

    $: {
        const result = [];
        const map = $taskStore.entities;
        for (let i = 0; i < dependencies.length; i++) {
            const dependency = dependencies[i];

            const fromTask = map[dependency.fromId];
            const toTask = map[dependency.toId];
            if(
                fromTask && toTask 
                && Math.min(fromTask.top, toTask.top) <= paddingTop + $visibleHeight 
                && Math.max(fromTask.top, toTask.top) >= paddingTop
            ) {
                result.push(dependency);
            }
        }
        visibleDependencies = result;
    }

</script>

<div class="dependency-container">
    {#each visibleDependencies as dependency (dependency.id)}
        <Dependency {...dependency}/>
    {/each}
</div>

<style>
    .dependency-container {
        position: absolute;
        width: 100%;
        height: 100%;
        
        pointer-events: none;
        top: 0;
        float: left;
        overflow: hidden;
        z-index: 0;
    }
</style>