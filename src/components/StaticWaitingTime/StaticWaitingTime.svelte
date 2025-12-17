<script>
	// import depedencies
	import { onMount } from 'svelte';

	// Import files
	import IdfmPrimToken from '$lib/IdfmPrimToken.json';

	// Import components
	import TimesValues from '../TimesValues/TimesValues.svelte';

	// Export props
	let { LineRef, LineName, StopRef, StopName, Destination, Color, WalkTime, Height } = $props();

	// Initialise variables
	let Query =
		'https://prim.iledefrance-mobilites.fr/marketplace/stop-monitoring?MonitoringRef=' +
		encodeURIComponent(StopRef) +
		'&LineRef=' +
		encodeURIComponent(LineRef);
	let QueryResponse = null;
	let WaitingTimes = $state([]);

	onMount(async () => {
		try {
			// Call to waiting times API
			const response = await fetch(Query, {
				method: 'GET',
				headers: {
					Accept: 'application/json',
					apikey: IdfmPrimToken.Token
				}
			});
			if (!response.ok) {
				throw new Error(
					`[!] An error has occured while establishing connexion with API. Status HTTP${response.status}`
				);
			}
			// Receive response from waiting times API
			const ResponseData = await response.json();
			// Convert absolute dates and times to delay from current time
			for (let i = 0; i < 2; i++) {
				let RawWaitingTime = new Date(
					ResponseData.Siri.ServiceDelivery.StopMonitoringDelivery[0].MonitoredStopVisit[i]
						.MonitoredVehicleJourney.MonitoredCall.ExpectedArrivalTime
				);
				let Now = new Date();
				let Time = Math.floor((RawWaitingTime - Now) / 60000);
				if (Time < 1) {
					Time = 0;
				}
				WaitingTimes = [...WaitingTimes, Time];
			}
		} catch (error) {
			console.log(
				'[!] An error has occured while establishing connexion with API or parsing its response. ',
				error.message
			);
		}
	});
</script>

<div class="Line" style="height: {Height}px;">
	<div class="SideBar" style="background-color: #{Color}"></div>

	<div class="Description">
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

		<div class="Destination">{Destination}</div>
	</div>

	<TimesValues Values={WaitingTimes} />
</div>

<style>
	.Line {
		background-color: white;
		width: 100%;
		border-radius: 25px;
		display: flex;
		align-items: center;
		margin-bottom: 25px;
		gap: 25px;
		height: 100%;
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
