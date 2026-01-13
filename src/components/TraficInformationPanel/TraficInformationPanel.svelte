<script>
	// import depedencies
	import { onMount } from 'svelte';
	import { htmlToText } from 'html-to-text';

	// Import files
	import IdfmPrimToken from '$lib/IdfmPrimToken.json';
	import SelectedTraficInformation from '$lib/SelectedTraficInformation.json';

	// Update time on clock
	let ClockString = $state([]);
	ClockString = '--:--:--';
	function UpdateClock() {
		let Now = new Date();
		let Hours = String(Now.getHours()).padStart(2, '0');
		let Minutes = String(Now.getMinutes()).padStart(2, '0');
		let Seconds = String(Now.getSeconds()).padStart(2, '0');
		ClockString = `${Hours}:${Minutes}:${Seconds}`;
	}
	setInterval(UpdateClock, 1000);

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

			// Select only disruptions lists that deal with the selected lines
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
							let TraficInformationItem = {
								DisruptionRef: Disruption.id,
								LineRef: DisruptionsListPerLine.LineRef,
								LineName: DisruptionsListPerLine.LineName,
								Color: DisruptionsListPerLine.Color,
								Type: Disruption.cause.toLowerCase(),
								Severity: Disruption.severity,
								Title: htmlToText(Disruption.title),
								Description: htmlToText(Disruption.message),
								DateUpdate: Disruption.lastUpdate
							};
							TraficInformation.push(TraficInformationItem);
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
			let CurrentDescription = document.getElementById(
				'Description' + TraficInformation[i].DisruptionRef
			);

			CurrentDescription.style.display = 'inline';
			await Sleep(0.1);
			CurrentDescription.style.opacity = '1';
			ColorBars.forEach((ColorBar) => {
				ColorBar.style.backgroundColor = '#' + TraficInformation[i].Color;
			});
			ColorBottomBar.style.borderBottom = '25px solid #' + TraficInformation[i].Color;
			await Sleep(8);
			CurrentDescription.style.opacity = '0';
			await Sleep(1);
			CurrentDescription.style.display = 'none';
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
	<div class="Clock">{ClockString}</div>
	<div class="Tabs">
		<div class="FirstTab">
			<img class="FirstTabLine" src="/assets/images/Lines/LogoLightTrain8.svg" />
			<img class="FirstTabEvent" src="/assets/images/LogoTraficPerturbation.svg" />
		</div>
		<div class="OtherTabs">
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficPerturbation.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficWorking.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficNightWorking.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficInformation.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain11.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficWorking.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain11.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficPerturbation.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/Lines/LogoLightTrain11.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficNightWorking.svg" />
			</div>
		</div>
	</div>
	<div class="Descriptions">
		<div class="ColorBarLeft"></div>
		<div class="ColorBarRight"></div>
		{#each TraficInformation as TraficInformationItem}
			<div class="Description" id={'Description' + TraficInformationItem.DisruptionRef}>
				<div class="Details">{TraficInformationItem.Description}</div>
			</div>
		{/each}

		<img class="LogoLMU" src="/assets/images/LogoLMU.png" />
		<img class="LogoIDFM" src="/assets/images/LogoIDFM.png" />
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

	.Tabs {
		height: 100px;
		width: 100%;
		padding: 0 25px 0px 62.5px;
		position: relative;
	}

	.FirstTab {
		height: 125px;
		width: 125px;
		background-color: white;
		border-top-left-radius: 10px;
		border-top-right-radius: 10px;
		position: absolute;
		top: 0;
		left: 25px;
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 100;
	}

	.FirstTabLine {
		height: 70%;
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

	.FirstTabEvent {
		height: 37.5px;
		bottom: 10px;
		right: 12.5px;
		position: absolute;
		animation: InfoTab1EventFlash 2s infinite;
	}

	.OtherTabs {
		height: 100%;
		width: 100%;
		display: flex;
		flex-wrap: nowrap;
	}

	.OtherTab {
		height: 100%;
		width: 100px;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
	}

	.OtherTabLine {
		height: 50%;
	}

	.OtherTabEvent {
		height: 25px;
		bottom: 12.5px;
		right: 12.5px;
		position: absolute;
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
		position: relative;
	}

	.ColorBarLeft {
		background-color: #ff5a00;
		height: 25px;
		position: absolute;
		top: 0;
		left: 0;
		border-bottom-right-radius: 10px;
		width: 25px;
	}

	.ColorBarRight {
		background-color: #ff5a00;
		height: 25px;
		position: absolute;
		top: 0;
		left: 150px;
		border-bottom-left-radius: 10px;
		width: calc(100% - 150px);
	}

	.Title {
		font-family: 'Bold';
		font-size: 40pt;
		color: #25303b;
	}

	.Description {
		display: none;
		opacity: 0;
		transition: opacity 1s ease-in-out;
	}

	.Details {
		font-family: 'Regular';
		font-size: 35pt;
		color: #25303b;
	}

	.LogoLMU {
		border-radius: 25px;
		height: 100px;
		opacity: 0.5;
		position: absolute;
		bottom: 25px;
		right: 150px;
	}

	.LogoIDFM {
		border-radius: 25px;
		height: 100px;
		opacity: 0.25;
		position: absolute;
		bottom: 25px;
		right: 25px;
	}
</style>
