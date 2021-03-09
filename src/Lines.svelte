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
            /*data: {
                type: 'line',
                labels: ['1.1.2021','2.1.2021','3.1.2021'],
                datasets: [{
                    type: 'line',
                    label: 'set1',
                    data: [1,2,3],
                    yAxisID: 'first'
                },
                {
                    label: 'set2',
                    type: 'bar',
                    data: [5,3,99],
                    yAxisID: 'second',
                    backgroundColor: 'blue'
                }]
            },
            options: {
                scales: {
                    xAxes: [{
                        type: 'category',
                        //labels: ['1.1.2021','2.1.2021','3.1.2021']
                    }],
                    yAxes: [{
                        type: 'linear',
                        id: 'first'

                    },{
                        type: 'linear',
                        id: 'second'
                    }]
                }
            }*/
            data: data,
            options: options
        });

    })
</script>

<canvas id="{id}" width="5" height="1" aria-label="Line-Chart"></canvas>