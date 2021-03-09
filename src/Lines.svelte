<script>
    import { onMount } from 'svelte';
    import Chart from 'chart.js';

    export let data;
    export let options;
    export let id;

    onMount(() => {
        console.log(data)
        const ctx = document.getElementById(id);
        let chart = new Chart(ctx, {
            data: data,
            options: options
        });

    })
</script>

<canvas id="{id}" width="5" height="1" aria-label="Line-Chart"></canvas>