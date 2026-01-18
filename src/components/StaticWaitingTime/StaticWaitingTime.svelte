<script>
	// import depedencies
	import { onMount } from 'svelte';

	// Import files
	import IdfmPrimToken from '$lib/IdfmPrimToken.json';
	import LogoPedestrianTransit from '$lib/assets/LogoPedestrianTransit.svelte';

	// Import components
	import TimesValues from '../TimesValues/TimesValues.svelte';

	// Export props
	let {
		LineRef,
		LineName,
		StopRef,
		StopName,
		Destination,
		Color,
		WalkTime,
		ShowDestination,
		Height
	} = $props();

	// Initialise variables
	let Query =
		'https://prim.iledefrance-mobilites.fr/marketplace/stop-monitoring?MonitoringRef=' +
		encodeURIComponent(StopRef) +
		'&LineRef=' +
		encodeURIComponent(LineRef);
	let QueryResponse = null;
	let WaitingTimes = $state([]);
	let Destinations = $state([]);

	async function FetchWaitingTimes() {
		try {
			// Call and response for waiting times API
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
			const ResponseData = await response.json();

			// Convert absolute dates and times to delay from current time
			let NewWaitingTimes = [];
			let NewDestinations = [];
			for (let i = 0; i < 2; i++) {
				let RawWaitingTime = new Date(
					ResponseData.Siri.ServiceDelivery.StopMonitoringDelivery[0].MonitoredStopVisit[i]
						.MonitoredVehicleJourney.MonitoredCall.ExpectedDepartureTime
				);
				let RawDestination = '';
				if (ShowDestination == '1') {
					RawDestination =
						ResponseData.Siri.ServiceDelivery.StopMonitoringDelivery[0].MonitoredStopVisit[i]
							.MonitoredVehicleJourney.DestinationShortName[0].value;
				}
				let Now = new Date();
				let Time = Math.floor((RawWaitingTime - Now) / 60000);
				if (Time < 1) {
					Time = 0;
				}
				NewWaitingTimes.push(Time);
				NewDestinations.push(RawDestination);
			}
			WaitingTimes = NewWaitingTimes;
			Destinations = NewDestinations;
		} catch (error) {
			console.log(
				'[!] An error has occured while establishing connexion with API or parsing its response. ',
				error.message
			);
		}
	}

	onMount(async () => {
		FetchWaitingTimes();
		const interval = setInterval(FetchWaitingTimes, 15000);
		return () => clearInterval(interval);
	});
</script>

<div class="Line" style="height: {Height}px;">
	<div class="SideBar" style="background-color: #{Color}"></div>

	<div class="Description">
		{#if WalkTime !== '0'}
			<div class="Icons">
				<img
					src="/assets/images/Lines/Logo{LineName}.svg"
					alt="Logo of {LineName} transport"
					class="Icon"
				/>
				<LogoPedestrianTransit style="height: 100%; color: #25303b;" />
				<div class="WalkTime">{WalkTime} min</div>
			</div>
		{/if}

		<div class="Destination">{Destination}</div>
	</div>

	<TimesValues Values={WaitingTimes} Destination={Destinations} />
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
