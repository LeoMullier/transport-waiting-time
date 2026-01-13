<script>
	// Import depedencies
	import { onMount, onDestroy, tick } from 'svelte';

	// Import files
	import SelectedWaitingTimes from '$lib/SelectedWaitingTimes.json';

	// Import components
	import StaticWaitingTime from '../StaticWaitingTime/StaticWaitingTime.svelte';
	import DynamicWaitingTime from '../DynamicWaitingTime/DynamicWaitingTime.svelte';

	// Initialise variables
	let StaticWaitingLines = SelectedWaitingTimes.Static;
	let DynamicWaitingLines = SelectedWaitingTimes.Dynamic;
	let NumberLines = StaticWaitingLines.length + DynamicWaitingLines.length;
	let NumberDisplayedLines = 3;
	let NumberDisplayedDynamicLines = NumberDisplayedLines - StaticWaitingLines.length;

	// Calculate heights of static and dynamic lines
	let HeightPanel = 880;
	let HeightLine =
		880 / NumberDisplayedLines - (25 * (NumberDisplayedLines + 1)) / NumberDisplayedLines;
	let HeightDynamicLines =
		NumberDisplayedDynamicLines * HeightLine + (NumberDisplayedDynamicLines - 1) * 25;

	let BlockToScroll;
	let Occurence = 0;
	function ScrollDynamicLines() {
		let FirstLine = BlockToScroll.firstElementChild;
		FirstLine.style.marginTop = '0px';
		let NumberScroll = DynamicWaitingLines.length;
		//for (let i = 0; i < NumberScroll + 2; i++) {
		FirstLine.style.marginTop =
			parseFloat(getComputedStyle(FirstLine).marginTop) - (HeightLine + 25) + 'px';
		Occurence++;
		if (Occurence >= NumberScroll) {
			setTimeout(() => {
				Occurence = 0;
				FirstLine.style.transition = 'none';
				setTimeout(() => {
					FirstLine.style.marginTop = '0px';
					setTimeout(() => {
						FirstLine.style.transition = '';
					}, 500);
				}, 500);
			}, 2000);
		}
	}

	let intervalId;
	onMount(async () => {
		await tick();
		intervalId = setInterval(() => {
			ScrollDynamicLines();
		}, 5000); // toutes les 5 secondes
	});

	onDestroy(() => {
		clearInterval(intervalId);
	});
</script>

<div class="Panel">
	<div class="StaticLines">
		{#each StaticWaitingLines as StaticWaitingLinesItem}
			<StaticWaitingTime
				LineRef={StaticWaitingLinesItem.LineRef}
				LineName={StaticWaitingLinesItem.LineName}
				StopRef={StaticWaitingLinesItem.StopRef}
				StopName={StaticWaitingLinesItem.StopName}
				Destination={StaticWaitingLinesItem.Destination}
				Color={StaticWaitingLinesItem.Color}
				WalkTime={StaticWaitingLinesItem.WalkTime}
				ShowDestination={StaticWaitingLinesItem.ShowDestination}
				Height={HeightLine}
			/>
		{/each}
	</div>
	<div class="DynamicLines" bind:this={BlockToScroll} style="height: {HeightDynamicLines}px;">
		{#each DynamicWaitingLines as DynamicWaitingLinesItem}
			<DynamicWaitingTime
				LineRef={DynamicWaitingLinesItem.LineRef}
				LineName={DynamicWaitingLinesItem.LineName}
				StopRef={DynamicWaitingLinesItem.StopRef}
				StopName={DynamicWaitingLinesItem.StopName}
				Destination={DynamicWaitingLinesItem.Destination}
				Color={DynamicWaitingLinesItem.Color}
				WalkTime={DynamicWaitingLinesItem.WalkTime}
				ShowDestination={DynamicWaitingLinesItem.ShowDestination}
				Height={HeightLine}
			/>
		{/each}

		{#each DynamicWaitingLines.slice(0, 2) as DynamicWaitingLinesItem}
			<DynamicWaitingTime
				LineRef={DynamicWaitingLinesItem.LineRef}
				LineName={DynamicWaitingLinesItem.LineName}
				StopRef={DynamicWaitingLinesItem.StopRef}
				StopName={DynamicWaitingLinesItem.StopName}
				Destination={DynamicWaitingLinesItem.Destination}
				Color={DynamicWaitingLinesItem.Color}
				WalkTime={DynamicWaitingLinesItem.WalkTime}
				Height={HeightLine}
			/>
		{/each}
	</div>
</div>

<style>
	.Panel {
		width: 50%;
		height: 880px;
		padding: 25px;
		padding-right: calc(25px / 2);
	}

	.StaticLines {
	}

	.DynamicLines {
		width: 100%;
		overflow-y: hidden;
		border-radius: 25px;
	}
</style>
