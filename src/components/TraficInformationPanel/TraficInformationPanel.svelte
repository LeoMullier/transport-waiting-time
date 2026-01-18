<script>
	// import depedencies
	import { onMount } from 'svelte';
	import { htmlToText } from 'html-to-text';

	// Import files
	import IdfmPrimToken from '$lib/IdfmPrimToken.json';
	import SelectedTraficInformation from '$lib/SelectedTraficInformation.json';
	import InformationIcon from '$lib/assets/Traffic/Information.svelte';
	import WorkingIcon from '$lib/assets/Traffic/Working.svelte';
	import PerturbationIcon from '$lib/assets/Traffic/Perturbation.svelte';

	// Update time on clock
	let ClockHours = $state([]);
	ClockHours = '--';
	let ClockMinutes = $state([]);
	ClockMinutes = '--';
	let ClockSeconds = $state([]);
	ClockSeconds = '--';

	function UpdateClock() {
		let Now = new Date();
		ClockHours = String(Now.getHours()).padStart(2, '0');
		ClockMinutes = String(Now.getMinutes()).padStart(2, '0');
		ClockSeconds = String(Now.getSeconds()).padStart(2, '0');
	}
	setInterval(UpdateClock, 1000);

	// Function to parse date format from API to JS object : year, month (0-based), day, hour, minute, second
	function ParseApiDate(ApiDate) {
		return new Date(
			ApiDate.slice(0, 4),
			ApiDate.slice(4, 6) - 1,
			ApiDate.slice(6, 8),
			ApiDate.slice(9, 11),
			ApiDate.slice(11, 13),
			ApiDate.slice(13, 15)
		);
	}

	// Function to test if an application period is in 48h or less than now
	function IsApplicationPeriodRelevant(ApplicationPeriod, Now = new Date()) {
		let Begin = ParseApiDate(ApplicationPeriod.begin);
		let End = ParseApiDate(ApplicationPeriod.end);

		let NowTime = Now.getTime();
		let BeginTime = Begin.getTime();
		let EndTime = End.getTime();

		let RelevantDelay = 48 * 60 * 60 * 1000; //48h

		let IsOngoing = BeginTime <= NowTime && EndTime >= NowTime;
		let IsStartingSoon = BeginTime > NowTime && BeginTime - NowTime <= RelevantDelay;

		return IsOngoing || IsStartingSoon;
	}

	// Convert names of lines for API
	let TraficInformation = $state([]); // LineRef, LineName, Color, Type, Severity, Title, Description, DateUpdate
	let TraficInformationSelectedLines = []; // LineRef, LineName, Color
	SelectedTraficInformation.forEach((SelectedTraficInformationItem, Index) => {
		let LineRef = SelectedTraficInformationItem.LineRef.match(/^STIF:Line::([^:]+):$/)[1];
		let TraficInformationSelectedLine = {
			LineRef: 'line:IDFM:' + LineRef,
			LineName: SelectedTraficInformationItem.LineName,
			Color: SelectedTraficInformationItem.Color
		};
		TraficInformationSelectedLines.push(TraficInformationSelectedLine);
	});

	// Call API and get its response for trafic information
	let Query = 'https://prim.iledefrance-mobilites.fr/marketplace/disruptions_bulk/disruptions/v2';
	let QueryResponse = null;
	async function FetchTraficInformation() {
		TraficInformation.length = 0;
		try {
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

			// Select only disruptions lists that deal with the selected lines and desired application period
			let DisruptionsListPerLines = [];
			TraficInformationSelectedLines.forEach((TraficInformationSelectedLine, IndexLine) => {
				ResponseData.lines.forEach((TraficInformationLine, IndexList) => {
					if (TraficInformationLine.id == TraficInformationSelectedLine.LineRef) {
						let DisruptionsListPerLine = {
							LineRef: TraficInformationSelectedLine.LineRef,
							LineName: TraficInformationSelectedLine.LineName,
							Color: TraficInformationSelectedLine.Color,
							DisruptionsList: TraficInformationLine.impactedObjects[0].disruptionIds
						};
						DisruptionsListPerLines.push(DisruptionsListPerLine);
					}
				});
			});

			// Get disruptions details
			DisruptionsListPerLines.forEach((DisruptionsListPerLine) => {
				DisruptionsListPerLine.DisruptionsList.forEach((DisruptionListItem) => {
					ResponseData.disruptions.forEach((Disruption) => {
						if (Disruption.id == DisruptionListItem) {
							let IsRelevant = false;
							Disruption.applicationPeriods.forEach((ApplicationPeriod) => {
								if (IsApplicationPeriodRelevant(ApplicationPeriod)) {
									IsRelevant = true;
								}
							});
							if (IsRelevant) {
								let TraficInformationItem = {
									DisruptionRef: Disruption.id,
									LineRef: DisruptionsListPerLine.LineRef,
									LineName: DisruptionsListPerLine.LineName,
									Color: DisruptionsListPerLine.Color,
									Type: Disruption.cause.toLowerCase(),
									Severity: Disruption.severity.toLowerCase(),
									Description: htmlToText(Disruption.message)
										.replace(/\s+/g, ' ')
										.replace(/\s+\./g, '.')
										.trim(),
									DateUpdate: Disruption.lastUpdate
								};
								TraficInformation.push(TraficInformationItem);
							}
						}
					});
				});
			});
		} catch (error) {
			console.log(
				'[!] An error has occured while establishing connexion with API or parsing its response. ',
				error.message
			);
		}
	}

	// Function te wait x seconds
	function Sleep(Secondes) {
		return new Promise((resolve) => setTimeout(resolve, Secondes * 1000));
	}

	// Function to display one trafic information, then the next one, etc
	async function RotateTraficInformation() {
		let ColorBars = [
			document.getElementsByClassName('ColorBarLeft')[0],
			document.getElementsByClassName('ColorBarRight')[0]
		];
		let ColorBottomBar = document.getElementsByClassName('Descriptions')[0];

		for (let i = 0; i < TraficInformation.length; i++) {
			if (i == 0) {
				await FetchTraficInformation();
				console.log('fetch');
			}

			// Initialisation
			let FirstTabs = document.getElementsByClassName('FirstTabs')[0];
			let CurrentFirstTab = FirstTabs.children[0];
			let NextFirstTab = FirstTabs.children[1];
			let FirstTabToAdd = CurrentFirstTab.cloneNode(true);

			let OtherTabs = document.getElementsByClassName('OtherTabs')[0];
			let CurrentOtherTab = OtherTabs.children[0];
			let NextOtherTab = OtherTabs.children[1];
			let OtherTabToAdd = CurrentOtherTab.cloneNode(true);

			let CurrentDescription = document.getElementById(
				'Description' + TraficInformation[i].DisruptionRef
			);
			let NextDescription = document.getElementById(
				'Description' + TraficInformation[(i + 1) % TraficInformation.length].DisruptionRef
			);

			// Closing current information
			await Sleep(12);
			CurrentFirstTab.style.opacity = '0';
			OtherTabToAdd.style.opacity = '0';
			CurrentDescription.style.opacity = '0';
			await Sleep(1);
			CurrentDescription.style.display = 'none';

			// Opening the next information
			NextDescription.style.display = 'inline';
			await Sleep(0.1);
			CurrentFirstTab.style.width = '0px';
			CurrentOtherTab.style.width = '0px';
			NextDescription.style.opacity = '1';
			ColorBars.forEach((ColorBar) => {
				ColorBar.style.backgroundColor =
					'#' + TraficInformation[(i + 1) % TraficInformation.length].Color;
			});
			ColorBottomBar.style.borderBottom =
				'25px solid #' + TraficInformation[(i + 1) % TraficInformation.length].Color;

			// Removing previous information
			await Sleep(1);
			FirstTabs.removeChild(CurrentFirstTab);
			FirstTabs.append(FirstTabToAdd);
			OtherTabs.removeChild(CurrentOtherTab);
			OtherTabs.append(OtherTabToAdd);

			// Recreating the list with last element
			await Sleep(0.1);
			FirstTabToAdd.style.opacity = '1';
			OtherTabToAdd.style.opacity = '1';

			await Sleep(1);
			if (i == TraficInformation.length - 1) {
				i = -1;
			}
		}
	}

	onMount(async () => {
		await FetchTraficInformation();
		//const interval = setInterval(FetchTraficInformation, 60000);
		RotateTraficInformation();
		return () => clearInterval(interval);
	});
</script>

<div class="Panel">
	<div class="Clock">
		{ClockHours}<span class="ClockDots">:</span>{ClockMinutes}<span class="ClockDots">:</span
		>{ClockSeconds}
	</div>
	<div class="Tabs">
		<div class="FirstTabsZone">
			<div class="FirstTabsVisible">
				<div class="FirstTabs">
					{#each TraficInformation as TraficInformationItem}
						<div class="FirstTab">
							<img
								class="FirstTabLine"
								src={'/assets/images/Lines/Logo' + TraficInformationItem.LineName + '.svg'}
								alt={'Logo of the ' +
									TraficInformationItem.LineName +
									'line of Paris transports system.'}
							/>
							{#if TraficInformationItem.Type == 'information' || TraficInformationItem.Severity == 'information'}
								<div class="TrafficIcon TrafficInformation">
									<InformationIcon style="height: 100%;" />
								</div>
							{:else if TraficInformationItem.Type == 'travaux'}
								<div class="TrafficIcon TrafficWorking">
									<WorkingIcon style="height: 85%;" />
								</div>
							{:else}
								{#if TraficInformationItem.Type == 'perturbation' && TraficInformationItem.Severity == 'perturbee'}
									<div class="TrafficIcon TrafficPerturbation">
										<PerturbationIcon style="height: 100%;" />
									</div>
								{/if}
								{#if TraficInformationItem.Type == 'perturbation' && TraficInformationItem.Severity == 'bloquante'}
									<div class="TrafficIcon TrafficDisruption">
										<PerturbationIcon style="height: 100%;" />
									</div>
								{/if}
							{/if}
						</div>
					{/each}
				</div>
			</div>
		</div>
		<div class="OtherTabs">
			{#each TraficInformation as TraficInformationItem}
				<div class="OtherTab">
					<img
						class="OtherTabLine"
						src={'/assets/images/Lines/Logo' + TraficInformationItem.LineName + '.svg'}
						alt={'Logo of the ' +
							TraficInformationItem.LineName +
							'line of Paris transports system.'}
					/>

					{#if TraficInformationItem.Type == 'information' || TraficInformationItem.Severity == 'information'}
						<div class="TrafficIcon TrafficIconSmall TrafficInformation">
							<InformationIcon style="height: 100%;" />
						</div>
					{:else if TraficInformationItem.Type == 'travaux'}
						<div class="TrafficIcon TrafficIconSmall TrafficWorking">
							<WorkingIcon style="height: 85%;" />
						</div>
					{:else}
						{#if TraficInformationItem.Type == 'perturbation' && TraficInformationItem.Severity == 'perturbee'}
							<div class="TrafficIcon TrafficIconSmall TrafficPerturbation">
								<PerturbationIcon style="height: 100%;" />
							</div>
						{/if}
						{#if TraficInformationItem.Type == 'perturbation' && TraficInformationItem.Severity == 'bloquante'}
							<div class="TrafficIcon TrafficIconSmall TrafficDisruption">
								<PerturbationIcon style="height: 100%;" />
							</div>
						{/if}
					{/if}
				</div>
			{/each}
		</div>
	</div>
	<div class="Descriptions">
		<div class="ColorBarLeft"></div>
		<div class="ColorBarRight"></div>
		{#each TraficInformation as TraficInformationItem, Index}
			{#if Index === 0}
				<div
					class="Description"
					style="display:inline; opacity:1;"
					id={'Description' + TraficInformationItem.DisruptionRef}
				>
					<div class="Details">{TraficInformationItem.Description}</div>
				</div>
			{:else}
				<div class="Description" id={'Description' + TraficInformationItem.DisruptionRef}>
					<div class="Details">{TraficInformationItem.Description}</div>
				</div>
			{/if}
		{/each}

		<img
			class="LogoLMU"
			src="/assets/images/LogoLMU.png"
			alt="Logo of the author of this web interface"
		/>
		<img
			class="LogoIDFM"
			src="/assets/images/LogoIDFM.png"
			alt="Logo of Île-de-France Mobilités transport company"
		/>
	</div>
</div>

<style>
	.Panel {
		background-color: lightgray;
		width: calc(50% - (25px * 1.5));
		height: calc(100% - 200px - (2 * 25px));
		margin: 25px;
		margin-left: calc(25px / 2);
		border-radius: 25px;
		position: relative;
		padding-top: 125px;
		overflow: hidden;
	}

	.Clock {
		background-color: black;
		color: #f9c823;
		border-bottom-left-radius: 25px;
		border-bottom-right-radius: 25px;
		text-align: center;
		font-family: 'Regular';
		font-size: 50pt;
		position: absolute;
		top: 0;
		right: 25px;
		width: 350px;
		height: 100px;
		padding-top: 10px;
	}

	.ClockDots {
		opacity: 0.5;
		font-family: 'Regular';
		font-size: 50pt;
		color: #f9c823;
	}

	.Tabs {
		height: 100px;
		width: 100%;
		padding: 0 25px 0px 80px;
		position: relative;
	}

	.FirstTabsZone {
		height: 125px;
		width: 125px;
		background-color: white;
		border-top-left-radius: 10px;
		border-top-right-radius: 10px;
		position: absolute;
		top: 0;
		left: 25px;
		z-index: 100;
		padding: 18.75px 10px 0 10px;
	}

	.FirstTabsVisible {
		display: flex;
		justify-content: flex-start;
		align-items: center;
		height: 100%;
		width: 100%;
		overflow: hidden;
	}

	.FirstTabs {
		display: flex;
		justify-content: flex-start;
		align-items: center;
		height: 100%;
		margin-left: -10px;
	}
	.FirstTab {
		display: flex;
		justify-content: center;
		align-items: flex-start;
		flex-shrink: 0;
		transition:
			width 1s ease-in-out,
			opacity 1s ease-in-out;
		height: 100%;
		width: 125px;
		position: relative;
		opacity: 1;
	}

	.FirstTabLine {
		height: 87.5px;
	}

	@keyframes InfoTab1EventFlash {
		0% {
			opacity: 1;
		}
		50% {
			opacity: 1;
		}
		51% {
			opacity: 0.5;
		}
		100% {
			opacity: 0.5;
		}
	}

	.TrafficIcon {
		height: 45px;
		width: 45px;
		bottom: 0px;
		right: 12.5px;
		position: absolute;
		animation: InfoTab1EventFlash 2s infinite;
		z-index: 1000;
		border-radius: 50%;
		color: white;
	}

	.TrafficIconSmall {
		height: 27.5px;
		width: 27.5px;
		bottom: 12.5px;
		right: 10px;
		animation: none;
		z-index: unset;
	}

	.TrafficInformation {
		background-color: royalblue;
		display: flex;
		align-items: flex-start;
		justify-content: center;
	}

	.TrafficWorking {
		background-color: slategrey;
		display: flex;
		align-items: flex-start;
		justify-content: center;
		padding-top: 3.5%;
	}

	.TrafficPerturbation {
		background-color: darkorange;
		display: flex;
		align-items: flex-start;
		justify-content: center;
	}

	.TrafficDisruption {
		background-color: red;
		display: flex;
		align-items: flex-start;
		justify-content: center;
	}

	.OtherTabs {
		height: 100%;
		width: 100%;
		display: flex;
		flex-wrap: nowrap;
	}

	.OtherTab {
		height: 100%;
		width: 80px;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
		overflow: hidden;
		transition:
			width 1s ease-in-out,
			opacity 1s ease-in-out;
		opacity: 1;
		flex-shrink: 0;
	}

	.OtherTabLine {
		height: 50%;
	}

	.Descriptions {
		width: 100%;
		height: calc(100% - 100px);
		background-color: white;
		border-bottom-left-radius: 25px;
		border-bottom-right-radius: 25px;
		border-bottom: 25px solid #ff5a00;
		position: relative;
		padding: 50px 25px 25px 25px;
		transition: border-bottom 1s ease-in-out;
		overflow: hidden;
	}

	.ColorBarLeft {
		background-color: #ff5a00;
		height: 25px;
		position: absolute;
		top: 0;
		left: 0;
		border-bottom-right-radius: 10px;
		width: 25px;
		transition: background-color 1s ease-in-out;
	}

	.ColorBarRight {
		background-color: #ff5a00;
		height: 25px;
		position: absolute;
		top: 0;
		left: 150px;
		border-bottom-left-radius: 10px;
		width: calc(100% - 150px);
		transition: background-color 1s ease-in-out;
	}

	.Description {
		display: none;
		opacity: 0;
		transition: opacity 1s ease-in-out;
		z-index: 100;
		position: relative;
	}

	.Details {
		font-family: 'Regular';
		font-size: 35pt;
		color: #25303b;
		z-index: 100;
		position: relative;
	}

	.LogoLMU {
		border-radius: 25px;
		height: 100px;
		opacity: 0.5;
		position: absolute;
		bottom: 25px;
		right: 150px;
		z-index: 1;
	}

	.LogoIDFM {
		border-radius: 25px;
		height: 100px;
		opacity: 0.25;
		position: absolute;
		bottom: 25px;
		right: 25px;
		z-index: 1;
	}
</style>
