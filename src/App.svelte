<script context="module">
	import { addMessages, init, getLocaleFromPathname } from 'svelte-i18n';
	import de from './de.json';
	import fr from './fr.json';
	import it from './it.json';

	addMessages('de', de);
	addMessages('fr', fr);
	addMessages('it', it);

	init({
		fallbackLocale: 'de',
		initialLocale: getLocaleFromPathname(/corona-memory-(.*?)\//), //this can only be checked once it's embedded in the site
	})

</script>

<script>
	import Chart from 'svelte-frappe-charts';
	import 'anychart';
	import 'https://cdn.anychart.com/releases/8.7.1/js/anychart-tag-cloud.min.js';
	import { _ } from 'svelte-i18n';

	let chart = (async () => {
		let res = await fetch(`https://www.corona-memory.ch/api/items?per_page=999999&item_set_id=796`);
		let oData = await res.json();
		wordchart(oData);
		return oData;
	})();

	let graphVal = (chart, field) => {
		chart = chart.filter(i => i[field]); //check that field is set
		let dates = chart.map((i) => {
		    	try {
					return new Date(i[field]["@value"])
				} catch (e) {
					console.log(e)
				}
		});
		dates = dates.filter(i => i instanceof Date && !isNaN(i)); //filter for true dates
		const minDate = new Date(Math.min.apply(null,dates)); //get the earliest date in the array
		let xAxis = [];
		for (let d=minDate.getTime(); d < Date.now();d += (60 * 60 * 24 * 1000) ){ //create a field for every day from min to now
			xAxis.push(new Date(d));
		}
		const yAxis = xAxis.map(x => dates.filter(d => d.getTime() <= x.getTime()).length); //get a value for every day.
		const yBar = xAxis.map(x => dates.filter(d => d.getDate() === x.getDate() && d.getMonth() === x.getMonth()).length);
		return {
			labels: xAxis.map(d => d.toLocaleDateString()),
			datasets: [
				{
					name: $_('Contributions total'),
					values: yAxis,
					chartType: 'line'
				},
				{
					name: $_('Contributions daily'),
					values: yBar,
					chartType: 'bar'
				},
			]
		};
	};

	let pieVal = (chart, field) => {
		const xAxis = [];
		chart.forEach(i => {
			try {
				if (!xAxis.includes(i[field][0]["@value"])) {
					xAxis.push(i[field][0]["@value"]);
				}
			} catch (err) {
				console.log("item does not include language!")
				console.log(i)
			}
		});
		const yAxis = xAxis.map(lang => {
			let count = 0;

				chart.forEach(i => {
					try {
						if (i[field][0]["@value"] === lang) {
							count++
						}
					} catch (err) {
						console.log("item does not include language!")
						console.log(i)
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
					"to",
				"ich", "dich", "dir", "er", "sie", "es", "wir", "man", "sie",
				"je", "te", "toi", "il", "elle", "nous", "elles", "ils",
				"io", "lei", "loro", "egli", "lui", "ella", "essa", "noi", "esse", "essi",
					"me", "da", "mi", "mon", "zum", "als", "si", "ce", "aux", "einen", "vom",
				"aber",
				"als",
				"am",
				"an",
				"auch",
				"auf",
				"aus",
				"bei",
				"bin",
				"bis",
				"bist",
				"da",
				"dadurch",
				"daher",
				"darum",
				"das",
				"daß",
				"dass",
				"dein",
				"deine",
				"dem",
				"den",
				"der",
				"des",
				"dessen",
				"deshalb",
				"die",
				"dies",
				"dieser",
				"dieses",
				"doch",
				"dort",
				"du",
				"durch",
				"ein",
				"eine",
				"einem",
				"einen",
				"einer",
				"eines",
				"er",
				"es",
				"euer",
				"eure",
				"für",
				"hatte",
				"hatten",
				"hattest",
				"hattet",
				"hier 	hinter",
				"ich",
				"ihr",
				"ihre",
				"im",
				"in",
				"ist",
				"ja",
				"jede",
				"jedem",
				"jeden",
				"jeder",
				"jedes",
				"jener",
				"jenes",
				"jetzt",
				"kann",
				"kannst",
				"können",
				"könnt",
				"machen",
				"mein",
				"meine",
				"mit",
				"nach",
				"nachdem",
				"nein",
				"nicht",
				"nun",
				"oder",
				"seid",
				"sein",
				"seine",
				"sich",
				"sie",
				"sind",
				"soll",
				"sollen",
				"sollst",
				"sollt",
				"sonst",
				"soweit",
				"sowie",
				"und",
				"unser", 	"unsere",
				"unter",
				"vom",
				"von",
				"vor",
				"wann",
				"warum",
				"was",
				"weiter",
				"weitere",
				"wenn",
				"wer",
				"werde",
				"werden",
				"werdet",
				"weshalb",
				"wie",
				"wieder",
				"wieso",
				"wir",
				"wird",
				"wirst",
				"wo",
				"woher",
				"wohin",
				"zu",
				"zum",
				"zur",
				"über",
				"alors",
				"au",
				"aucuns",
				"aussi",
				"autre",
				"avant",
				"avec",
				"avoir",
				"bon",
				"car",
				"ce",
				"cela",
				"ces",
				"ceux",
				"chaque",
				"ci",
				"comme",
				"comment",
				"dans",
				"des",
				"du",
				"dedans",
				"dehors",
				"depuis",
				"devrait",
				"doit",
				"donc",
				"dos",
				"début",
				"elle",
				"elles",
				"en",
				"encore",
				"essai",
				"est",
				"et",
				"eu",
				"fait",
				"faites",
				"fois",
				"font",
				"hors",
				"ici",
				"il",
				"ils",
				"je 	juste",
				"la",
				"le",
				"les",
				"leur",
				"là",
				"ma",
				"maintenant",
				"mais",
				"mes",
				"mien",
				"moins",
				"mon",
				"mot",
				"même",
				"ni",
				"nommés",
				"notre",
				"nous",
				"ou",
				"où",
				"par",
				"parce",
				"pas",
				"peut",
				"peu",
				"plupart",
				"pour",
				"pourquoi",
				"quand",
				"que",
				"quel",
				"quelle",
				"quelles",
				"quels",
				"qui",
				"sa",
				"sans",
				"ses",
				"seulement",
				"si",
				"sien",
				"son",
				"sont",
				"sous",
				"soyez 	sujet",
				"sur",
				"ta",
				"tandis",
				"tellement",
				"tels",
				"tes",
				"ton",
				"tous",
				"tout",
				"trop",
				"très",
				"tu",
				"voient",
				"vont",
				"votre",
				"vous",
				"vu",
				"ça",
				"étaient",
				"état",
				"étions",
				"été",
				"être",
				"a",
				"adesso",
				"ai",
				"al",
				"alla",
				"allo",
				"allora",
				"altre",
				"altri",
				"altro",
				"anche",
				"ancora",
				"avere",
				"aveva",
				"avevano",
				"ben",
				"buono",
				"che",
				"chi",
				"cinque",
				"comprare",
				"con",
				"consecutivi",
				"consecutivo",
				"cosa",
				"cui",
				"da",
				"del",
				"della",
				"dello",
				"dentro",
				"deve",
				"devo",
				"di",
				"doppio",
				"due",
				"e",
				"ecco",
				"fare",
				"fine",
				"fino",
				"fra",
				"gente",
				"giu",
				"ha",
				"hai",
				"hanno",
				"ho",
				"il",
				"indietro 	invece",
				"io",
				"la",
				"lavoro",
				"le",
				"lei",
				"lo",
				"loro",
				"lui",
				"lungo",
				"ma",
				"me",
				"meglio",
				"molta",
				"molti",
				"molto",
				"nei",
				"nella",
				"no",
				"noi",
				"nome",
				"nostro",
				"nove",
				"nuovi",
				"nuovo",
				"o",
				"oltre",
				"ora",
				"otto",
				"peggio",
				"pero",
				"persone",
				"piu",
				"poco",
				"primo",
				"promesso",
				"qua",
				"quarto",
				"quasi",
				"quattro",
				"quello",
				"questo",
				"qui",
				"quindi",
				"quinto",
				"rispetto",
				"sara",
				"secondo",
				"sei",
				"sembra 	sembrava",
				"senza",
				"sette",
				"sia",
				"siamo",
				"siete",
				"solo",
				"sono",
				"sopra",
				"soprattutto",
				"sotto",
				"stati",
				"stato",
				"stesso",
				"su",
				"subito",
				"sul",
				"sulla",
				"tanto",
				"te",
				"tempo",
				"terzo",
				"tra",
				"tre",
				"triplo",
				"ultimo",
				"un",
				"una",
				"uno",
				"va",
				"vai",
				"voi",
				"volte",
				"vostro",
					"mich", "haben", "wurde", "dann", "diese", "wurden",


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
		<h2>{$_('Contributions')}</h2>
		<Chart data={graphVal(data, "o:created")} axisOptions={{xIsSeries:true, xAxisMode:'tick'}} lineOptions={{hideDots: 1}} type='axis-mixed' />
		<p>{$_('Text Sub-graph')}</p>
		<h2>{$_('Contributions sorted by langs')}</h2>
	<div class="grid">
		<div>
			<h3>{$_('Contributions sorted by lang')}</h3>
			<Chart data={pieVal(data, "dcterms:language")} type="pie" />
		</div>
		<div>
			<h3>{$_('Languagedistribution')}</h3>
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
		<p>{$_('Text Sub-cake')}</p>
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
	<h2>{$_('Wordcloud')}</h2>
	<div id="container"></div>
	<p>{$_('Text Sub-wordcloud')}</p>
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