<script>
	import * as d3 from 'd3';
	import * as topojson from 'topojson-client';
	import geoData from '$lib/data/counties-albers-10m.json';

	let {
		data,
		stylePositive = {
			fill: '#4169E1',
			fillOpacity: 0.7,
			stroke: '#fff',
			strokeWidth: 0.5,
			strokeDasharray: ''
		},
		styleNegative = {
			fill: 'none',
			fillOpacity: 0,
			stroke: '#4169E1',
			strokeWidth: 1,
			strokeDasharray: '2'
		},
		width = 975,
		height = 610
	} = $props();

	// Precompute static features
	const nation = topojson.feature(geoData, geoData.objects.nation);
	const statemap = new Map(
		topojson.feature(geoData, geoData.objects.states).features.map((d) => [d.id, d])
	);
	const statemesh = topojson.mesh(geoData, geoData.objects.states, (a, b) => a !== b);
	const path = d3.geoPath();

	function centroid(feature) {
		return path.centroid(feature);
	}

	// Process data
	let processedData = $derived(
		data.map((d) => ({
			...d,
			state: statemap.get(d.state_fips)
		}))
	);
	console.log(processedData);

	let radius = $derived(
		d3
			.scaleSqrt()
			.domain([0, d3.max(data, (d) => Math.abs(d.value))])
			.range([0, 40])
	);
</script>

<svg
	{width}
	{height}
	viewBox={`0 0 ${width} ${height}`}
	style="width: 100%; height: auto; height: intrinsic;"
>
	<!-- Static elements -->
	<path d={path(nation)} fill="#ddd" />
	<path d={path(statemesh)} fill="none" stroke="white" stroke-linejoin="round" />

	<!-- Bubbles -->
	{#each processedData as d}
		<g transform={`translate(${centroid(d.state)})`}>
			<circle
				r={radius(Math.abs(d.value))}
				style={Object.entries(d.value > 0 ? stylePositive : styleNegative)
					.map(
						([key, value]) =>
							`${key.replace(/([A-Z])/g, (g) => `-${g[0].toLowerCase()}`)}: ${value}`
					)
					.join(';')}
			/>
		</g>
	{/each}

	<!-- Legend -->
	<g
		fill="#777"
		transform={`translate(${width - 60},${height - 132})`}
		text-anchor="middle"
		style="font: 10px sans-serif"
	>
		{#each radius.ticks(4).slice(1) as tick}
			<g>
				<circle fill="none" stroke="#ccc" cy={-radius(tick)} r={radius(tick)} />
				<text y={-2 * radius(tick)} dy="1.3em">
					{radius.tickFormat(4, '%')(tick)}
				</text>
			</g>
		{/each}
	</g>
</svg>

<style>
	svg {
		margin: 0 auto;
		display: block;
	}
</style>
