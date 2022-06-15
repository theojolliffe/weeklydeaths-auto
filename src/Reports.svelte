<script>
	import { ord, ud, otherRank, otherEst, qui, cha, cur, figs, get_word } from "./utils";
	import robojournalist from 'robojournalist';
	import * as d3 from 'd3'

	var options, selected, place, rgn, topics;

	let data = {}
	d3.csv('./data.csv')
	.then((d) => {
		d.forEach(e => {
			data[e['Variable']] = {}
			data[e['Variable']][0] = parseFloat(e[0].replace("%", "").replace(",", ""))
			data[e['Variable']][1] = parseFloat(e[1].replace("%", "").replace(",", ""))
		})
	})
	$: console.log(data)

	let meta = {}
	d3.csv('./meta.csv')
	.then((d) => {
		d.forEach(e => {
			meta[e['issue']] = e['info']
		})
	})
	$: console.log(meta)

	var template;
    fetch("./template.pug")
        .then((res) => res.text())
        .then((txt) => (template = txt))

	let loaded = false
    const onRosaeNlgLoad = () => { loaded = true }
	options = [
		{'label': 'England', 'value': 'E92000001'},
		{'label': 'Wales', 'value': 'W92000004'},
	]

	// loadArea('Northumberland')

	function iterate(obj, pname) {
		Object.keys(obj).forEach(key => {
			if (typeof obj[key] === 'object') {
				iterate(obj[key], pname)
			} else {
				obj[key] = robojournalist(obj[key], {
					plcname: pname,
				})
			}
		})
	}

	function results(place, topicsIn) {
		
		return rosaenlg_en_US.render(template, {
			language: 'en_UK',
			data: data,
			meta: meta,
			get_word: get_word,
			figs: figs,
			otherEst: otherEst,
			cur: cur,
			cha: cha,
			qui: qui,
			otherRank: otherRank,
			ud: ud,
			ord: ord,
			wordob: wordob,
		})
	}

	let word1 = '';
	let word2 = '';
	let word3 = '';
	let word4 = '';

	let wordob = {
		word1: 'In the week to 2 June 2022',
		word2: 'tested positive for COVID-19',
		word3: 'the week',
		word4: 'testing positive',
	}

	function handleClick() {
		wordob['word1'] = word1
		wordob['word2'] = word2
		wordob['word3'] = word3
		wordob['word4'] = word4
		place = place
	}
	let rewrite = false;
	function handleRewrite() {
		rewrite = true
	}


</script>

<svelte:head>
	<script src="./rosaenlg_tiny_en_US_3.2.2_comp.js" on:load="{onRosaeNlgLoad}"></script>
</svelte:head>

<div>
	<div style="height: 50px;"></div>
	{#if loaded}
		{#if data}
			<main>
				{@html results(data, topics)}
			</main>
		{/if}
	{/if}
</div>


<style>
	@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;700&display=swap');
	:global(body) {
		font-family: 'Open Sans', sans-serif;
		padding: 0px;
		line-height: 2;
		color: #323132;
	}

	main {
		text-align: left;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		width: 640px
	}

	h1 {
		font-size: 3em;
		line-height: 1.5;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
	div#visph {
		background-color: #afcbd6;
		color: #003C57;
		height: 240px;
		padding: 80px;
		font-size: 3rem;
		font-weight: 600;
	}
	span.back-to-top {
		text-decoration: underline;
		color: #206095;
		cursor: pointer;
	}
	div#sf {
		background-color: #F5F5F6;
		padding: 15px;
		font-size: 1.2rem;
	}

</style>