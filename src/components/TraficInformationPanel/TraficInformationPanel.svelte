<script>
	// Import components

	// Update time on clock
	let ClockString = '--:--:--';
	function UpdateClock() {
		let Now = new Date();
		let Hours = String(Now.getHours()).padStart(2, '0');
		let Minutes = String(Now.getMinutes()).padStart(2, '0');
		let Seconds = String(Now.getSeconds()).padStart(2, '0');
		ClockString = `${Hours}:${Minutes}:${Seconds}`;
	}
	setInterval(UpdateClock, 1000);
</script>

<div class="Panel">
	<div class="Clock">{ClockString}</div>
	<div class="Tabs">
		<div class="FirstTab">
			<img class="FirstTabLine" src="/assets/images/LogoLightTrain8.svg" />
			<img class="FirstTabEvent" src="/assets/images/LogoTraficPerturbation.svg" />
		</div>
		<div class="OtherTabs">
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficPerturbation.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficWorking.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficNightWorking.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain8.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficInformation.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain11.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficWorking.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain11.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficPerturbation.svg" />
			</div>
			<div class="OtherTab">
				<img class="OtherTabLine" src="/assets/images/LogoLightTrain11.svg" />
				<img class="OtherTabEvent" src="/assets/images/LogoTraficNightWorking.svg" />
			</div>
		</div>
	</div>
	<div class="Description">
		<div class="ColorBarLeft"></div>
		<div class="ColorBarRight"></div>
		<span class="Title">Perturbation : </span>
		<span class="Details">Signaler à nos agents tout objet abandonné ou situation inhabituelle</span
		>
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

	.Description {
		width: 100%;
		height: calc(100% - 100px);
		background-color: white;
		border-bottom-left-radius: 25px;
		border-bottom-right-radius: 25px;
		border-bottom: 25px solid #6e6e00;
		position: relative;
		padding: 50px 25px 25px 25px;
		position: relative;
	}

	.ColorBarLeft {
		background-color: #6e6e00;
		height: 25px;
		position: absolute;
		top: 0;
		left: 0;
		border-bottom-right-radius: 10px;
		width: 25px;
	}

	.ColorBarRight {
		background-color: #6e6e00;
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

	.Details {
		font-family: 'Regular';
		font-size: 40pt;
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
