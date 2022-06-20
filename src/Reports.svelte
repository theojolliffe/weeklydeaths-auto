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

	let active = false, navigating = false, active2 = false;
	
	function simulateNavigation() {		
		navigating = true
		console.log("clicked", navigating);
		setTimeout(() => { // Reset
			navigating = false;
			active = false;
			active2 = false;
		}, 2000);
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


				<!-- THIS IS THE DISCLAIMER -->
				<div style="width: 180px; background: #f4f4f4; border-radius: 5px;" class:active on:mouseenter={() => {
						console.log("enter", navigating);
						if (!navigating) {
							active = true;
							active2 = true;
						}
					}}
					on:mouseleave={() => {
						console.log("leave", navigating);
						if (!navigating) {
							active = false;
							active2 = false;
						}
					}} on:click={simulateNavigation}>
					<div style="width: 15px; float: left; padding: 3px 6px 6px 16px;">
						<div class="css-1dbjc4n r-1habvwh r-18u37iz r-dnmrzs r-1ny4l3l">
							<div class="css-1dbjc4n r-14g73ha r-1d4mawv r-1bymd8e r-1b94p3d"><svg viewBox="0 0 24 24" aria-hidden="true" style="fill: rgb(136 136 136);"><g><path d="M12.22 1.75h-.44c-4.51 0-8.16 3.65-8.16 8.16v6.84c0 3.04 2.46 5.5 5.5 5.5h5.76c3.04 0 5.5-2.46 5.5-5.5V9.91c0-4.51-3.65-8.16-8.16-8.16zM8.61 13.52c-1.05 0-1.89-.85-1.89-1.9s.84-1.9 1.89-1.9c1.04 0 1.89.85 1.89 1.9s-.85 1.9-1.89 1.9zm6.78 0c-1.04 0-1.89-.85-1.89-1.9s.85-1.9 1.89-1.9c1.05 0 1.89.85 1.89 1.9s-.84 1.9-1.89 1.9zM23 9.17v5.66c0 .5-.41.91-.91.91h-.05c-.5 0-.91-.41-.91-.91V9.17c0-.51.41-.91.91-.91h.05c.5 0 .91.4.91.91zm-20.13 0v5.66c0 .5-.41.91-.91.91h-.05c-.5 0-.91-.41-.91-.91V9.17c0-.51.41-.91.91-.91h.05c.5 0 .91.4.91.91z"></path></g></svg></div>
						</div>
					</div>
					<div dir="auto">
						<span style="font-size: small; padding: 10px 10px 10px 10px; colour: rgb(136 136 136)">Semi-automated</span>
					</div>
				</div>
				<div style="width: 400px; font-size: small; display: none; padding: 5px;" class:active2 on:mouseenter={() => {
					console.log("enter", navigating);
						if (!navigating) {
							active2 = true;
						}
					}}
					on:mouseleave={() => {
						console.log("leave", navigating);
						if (!navigating) {
							active2 = false;
						}
					}} on:click={simulateNavigation}>
					<p>
						This content was created using some automation. 
						<a href="">
							Find out more about how the ONS use automation in digital publishing.
						</a>
					</p>
				</div>
			</main>
		{/if}
	{/if}
</div>


<style>
	.active {
		background:#ddd !important;
		display:block
	}
	.active2 {
		display: block !important;
	}
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