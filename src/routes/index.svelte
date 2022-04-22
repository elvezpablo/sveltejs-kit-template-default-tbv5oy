


<script>
	import {onMount} from "svelte"

	let stations = [];

	const loadStations = async () => {
		const response = await fetch('https://api.bart.gov/api/etd.aspx?cmd=etd&orig=16TH&key=MW9S-E7SL-26DU-VV8V&json=y');
		const {root} = await  response.json();
		const {station} = root;
		
		stations = station;
	};

	onMount(async () => {
		await loadStations();
	})

	setInterval(() => {		
		loadStations();
	}, 10*1000)

</script>

{#each stations as s}
	<div class="w-full">
		<h2 class="text-xl bg-gray-400 p-3">{s.name}</h2>
		{#each s.etd as e}
		<div>			
			<h3 class="ml-4 mt-3 mb-2 inline-block text-white px-2 py-1 rounded bg-sky-800/80">{e.destination}</h3>
			{#each  e.estimate as m}
			<div class="mx-8 border-b border-gray-500 flex justify-between">
				
				<div >									
					<svg class="mt-2 w-[100px] h-[10px]" viewBox="0 0 100 10">
						<rect width="100" height="10" rx="5" fill="rgb(100,100,100, .4)"  />
						
						{#if m.minutes <= 4}
						<rect width="100" height="10" rx="5"  fill="url(#diagonalHatch)" />
						<pattern id="diagonalHatch" patternUnits="userSpaceOnUse" width="4" height="4">
							<path d="M-1,1 l2,-2
											M0,4 l4,-4
											M3,5 l2,-2" 
										style="stroke:red; stroke-width:1" />
						</pattern>
						{/if}
						<rect width={Math.min(100, m.minutes / 40 * 100)} height="10" rx="5" fill={m.color} />
					</svg>						
				</div>

				<div >					
				{#if m.minutes == 4}
					<span class="border border-red-400 rounded px-1 py-[1px] text-xs text-red-600">run!</span>				
				{:else if m.minutes == 3}
					<span class="border border-red-400 rounded px-1 py-[1px] text-xs text-red-600">run faster!</span>				
				{:else if m.minutes == 2}
					<span class="border border-red-400 rounded px-1 py-[1px] text-xs text-red-600">you won't make it</span>				
				{:else if m.minutes == 1}
					<span class="border border-red-400 rounded px-1 py-[1px] text-xs text-red-600">don't try it</span>
				{/if}
				
				{m.minutes}
				</div>
				
				</div>
			{/each}
			</div>
		{/each}
	</div>
{/each}

