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
		const minDate = new Date(Math.min.apply(null,dates)); //get the earliest date in the array
		let xAxis = [];
		for (let d=minDate.getTime(); d < Date.now();d += (60 * 60 * 24 * 1000) ){ //create a field for every day from min to now
			xAxis.push(new Date(d));
		}
		const yAxis = xAxis.map((x) => dates.filter((d) => d.getTime() <= x.getTime()).length); //get a value for every day.
		const yBar = xAxis.map(x => dates.filter(d => d.getDate() === x.getDate() && d.getMonth() === x.getMonth()).length);
		console.log(dates[33].getDay());
		console.log(yBar);
		return {
			labels: xAxis.map(d => d.toLocaleDateString()),
			datasets: [
				{
					name: "Beiträge Gesamt",
					values: yAxis,
					chartType: 'line'
				},
				{
					name: "Beiträge Täglich",
					values: yBar,
					chartType: 'bar'
				},
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
					name: "Beiträge",
					values: yAxis
				}
			]
		};
	}

	const wordchart = ( (oData) => {
		oData = oData.filter(i => i['dcterms:abstract']);
		const data = [...oData.map(i => i['o:title']), ...oData.map(i => i['dcterms:abstract'][0]['@value'])].toString();

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
					"du",
					"au",
					"se",
					"so",
					"ad",
					"an",
					"on",
					"ils",
					"del",
					"um",
					"to"

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
		<h2>Gesamtbeiträge</h2>
		<Chart data={graphVal(data, "o:created")} axisOptions={{xIsSeries:true}} type='axis-mixed' />
		<p>Der Graph zeigt auf der X-Achse einen Zeitverlauf, auf der Y-Achse die Anzahl an Beiträgen auf Corona-Memory.ch</p>
		<h2>Beiträge nach Sprachen</h2>
	<div class="grid">
		<div>
			<h3>Beiträge nach Sprache</h3>
			<Chart data={pieVal(data, "dcterms:language")} type="pie" />
		</div>
		<div>
			<h3>Sprecher in der Schweiz</h3>
			<Chart data={{
				labels: ['de','fr','it'],
				datasets: [
					{
						name: "Sprecher",
						values: [4458156, 1619708, 593646]
					}
				]
			}} type="pie" />
		</div>
	</div>
		<p>Das Kuchendiagramm zeigt, in welcher Sprache unsere Beiträge verfasst sind. Als Referenz dazu sehen Sie die Zahlen der Sprecher der jeweiligen Sprachen in der Schweiz.(2018. Quelle: Bundesamt für Statistik)</p>
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
	<h2>Wörterwolke</h2>
	<div id="container"></div>
	<p>Die Wolke zeigt die meistbenutzten Wörter in den Beiträgen.</p>
</main>

<style>
#container {
	height: 800px;
}
	.grid {
		display: grid;
		grid-template-columns: 1fr 1fr;
	}
</style>