<script>
	import SignedStateBubbleMap from './SignedStateBubbleMap.svelte';
	import turnoutData from '$lib/data/bubblemap_turnout.json';
	let dataD = turnoutData.map((d) => {
		return { state_fips: d.state_fips, value: d.DEMOCRAT };
	});
	let dataR = turnoutData.map((d) => {
		return { state_fips: d.state_fips, value: d.REPUBLICAN };
	});
</script>

<h1>Voter Turnout</h1>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>
<h5>Here's Kamala's 2024 turnout compared to her 2020 run with Biden</h5>
<SignedStateBubbleMap data={dataD} />
<h5>And here's Trump's 2024 turnout compared to his 2020 run</h5>
<SignedStateBubbleMap
	data={dataR}
	stylePositive={{
		fill: 'brown',
		fillOpacity: 0.7,
		stroke: '#fff',
		strokeWidth: 0.5,
		strokeDasharray: ''
	}}
	styleNegative={{
		fill: 'none',
		fillOpacity: 0,
		stroke: 'brown',
		strokeWidth: 1,
		strokeDasharray: '2'
	}}
/>

<style>
</style>
