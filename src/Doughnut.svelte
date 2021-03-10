<script>
    import { onMount } from 'svelte';
    import Chart from 'chart.js';

    export let datasets;
    export let id;

    const data = datasets;
    const options = {

    }

    onMount(() => {
        const ctx = document.getElementById(id);
        let chart = new Chart(ctx, {
            type: 'doughnut',
            data: data,
            options: options
        });

    })
</script>

<canvas id="{id}" width="3" height="1" aria-label="Donut-Chart"></canvas>