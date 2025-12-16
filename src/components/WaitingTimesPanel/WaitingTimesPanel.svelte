<script>
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
				Height={HeightLine}
			/>
		{/each}
	</div>
	<div class="DynamicLines" style="height: {HeightDynamicLines}px;">
		{#each DynamicWaitingLines as DynamicWaitingLinesItem}
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
		overflow-y: auto;
		border-radius: 25px;
		padding-top: -25px;
	}
</style>
