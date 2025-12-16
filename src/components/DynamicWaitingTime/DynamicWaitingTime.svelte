<script>
	// Import components
	import TimesValues from '../TimesValues/TimesValues.svelte';

	// Export props
	let { LineRef, LineName, StopRef, StopName, Destination, Color, WalkTime, Height } = $props();
</script>

<div class="Line" style="height: {Height}px;">
	<div class="SideBar" style="background-color:#{Color}"></div>
	<div class="Description">
		<div class="Destination">{Destination}</div>

		{#if WalkTime !== '0'}
			<div class="Icons">
				<img
					src="/assets/images/Logo{LineName}.svg"
					alt="Logo of {LineName} transport"
					class="Icon"
				/>
				<img src="/assets/images/LogoPedestrianTransit.svg" alt="Logo of pedestrian" class="Icon" />
				<div class="WalkTime">{WalkTime} min</div>
			</div>
		{/if}
	</div>
	<TimesValues Values="12" />
</div>

<style>
	.Line {
		background-color: white;
		width: 100%;
		height: calc(100% - 25px);
		border-radius: 25px;
		display: flex;
		align-items: center;
		gap: 25px;
		margin-bottom: 25px;
		transition: margin-top 1s ease-in-out;
	}

	.SideBar {
		background-color: #25303b;
		height: 100%;
		width: 0px;
		border-top-left-radius: 25px;
		border-bottom-left-radius: 25px;
	}

	.Description {
		flex: 1;
		display: flex;
		flex-wrap: wrap;
		gap: 25px;
	}

	.Destination {
		font-size: 40pt;
		color: #25303b;
	}

	.Icons {
		width: 100%;
		height: 75px;
		display: flex;
		gap: 18.75px;
	}

	.Icon {
		height: 100%;
		color: #25303b;
	}

	.WalkTime {
		font-size: 25pt;
		color: #25303b;
		display: flex;
		align-items: flex-end;
		margin-left: -25px;
		margin-bottom: -2.5px;
	}
</style>
