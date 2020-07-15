<script>
	import Chart from 'svelte-frappe-charts';
	import { onMount } from 'svelte';
	import 'anychart';
	import 'https://cdn.anychart.com/releases/8.7.1/js/anychart-tag-cloud.min.js';

	let chart = (async () => {
		let res = await fetch(`https://www.corona-memory.ch/api/items?per_page=999999&item_set_id=796`);
		let oData = await res.json();
		wordchart(oData);
		return oData;
	})();

	let graphVal = (chart, field) => {
		chart = chart.filter(i => i[field]); //check that field is set
		let dates = chart.map((i) => new Date(i[field]["@value"]));
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
	};

	let pieVal = (chart, field) => {
		const xAxis = [];
		chart.forEach(i => {
			if (!xAxis.includes(i[field][0]["@value"])) {
				xAxis.push(i[field][0]["@value"]);
			}
		});
		const yAxis = xAxis.map(lang => {
			let count = 0;
			chart.forEach(i => {
				if (i[field][0]["@value"] === lang) {
					count++
				}
			});
			return count;
		})
		return {
			labels: xAxis,
			datasets: [
				{
					values: yAxis
				}
			]
		};
	}

	const wordchart = ( (oData) => {
		oData = oData.filter(i => i['dcterms:abstract']);
		const data = oData.map(i => i['dcterms:abstract'][0]['@value']).toString();

		// create a chart and set the data
		const wordChart = anychart.tagCloud();

		wordChart.data(data, {
			mode: "by-word",
			maxItems: 160,
			ignoreItems: [
				"the",
				"and",
				"he",
				"or",
				"of",
				"in",
				"thy",
					"der",
					"die",
					"das",
					"und",
					"et",
					"un",
					"à",
					"de",
					"la",
					"les",
					"le",
					"ein",
					"des",
					"en",
					"a",
					"es",
					"une",
					"von",
					"den",
					"è",
					"i",
					"al",
					"im",
					"vor",
					"bei",
					"sur",
					"zu",
					"dem",
					"pour",
					"que",
					"ich",
					"je",
					"di",
					"il",
					"e",
					"er",
					"ou",
					"mit",
					"auf",
					"che",
					"für",
					"per",
					"dans",
					"ma",
					"hat",
					"ces",
					"una",
					"du"

			]
		});

		// set the container id
		wordChart.container("container");

		// initiate drawing the chart
		wordChart.draw();
	});

</script>

<main>
	{#await chart}
		<p>...waiting</p>
	{:then data}
		<Chart data={graphVal(data, "o:created")} type="line" axisOptions={{xIsSeries:true}} />
		<Chart data={pieVal(data, "dcterms:language")} type="pie" />
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
	<div id="container"></div>
</main>

<style>
#container {
	height: 800px;
}
</style>