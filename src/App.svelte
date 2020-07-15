<script>
	import Chart from 'svelte-frappe-charts';
	import { onMount } from 'svelte';

	const field = "o:created";

	let chart = (async () => {
		let res = await fetch(`https://www.corona-memory.ch/api/items?per_page=999999&item_set_id=796`);
		let oData = await res.json();
		oData = oData.filter(i => i[field]); //check that field is set
		let dates = oData.map((i) => new Date(i[field]["@value"]));
		dates = dates.filter(i => i instanceof Date && !isNaN(i)); //filter for true dates
		const minDate = new Date(Math.min.apply(null,dates));
		let xAxis = [];
		for (let d=minDate.getTime(); d < Date.now();d += (60 * 60 * 24 * 1000) ){
			xAxis.push(new Date(d));
		}
		const yAxis = xAxis.map((x) => dates.filter((d) => d.getTime() <= x.getTime()).length);
		return {
			labels: xAxis.map(d => d.toLocaleDateString()),
			datasets: [
				{
					values: yAxis
				}
			]
		};
	})();

</script>

<main>
	{#await chart}
		<p>...waiting</p>
	{:then data}
		<Chart data={data} type="line" xAxisMode='tick' axisOptions={{xIsSeries:true}} />
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
</main>

<style>

</style>