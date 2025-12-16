<script>
	import './+page.css';
	import Header from '/src/components/Header/Header.svelte';
	import WaitingTimesPanel from '/src/components/WaitingTimesPanel/WaitingTimesPanel.svelte';
	import TraficInformationPanel from '/src/components/TraficInformationPanel/TraficInformationPanel.svelte';
</script>

<div class="Page">
	<Header
		MainTransportType="LightTrain"
		MainTransportLine="8"
		StationName="Saint-Denis - Porte de Paris"
		;
	/>

	<WaitingTimesPanel />

	<TraficInformationPanel />
</div>

<!-- 

	<div class="TimeBlock">
		<div class="TimeDynamic"></div>
	</div>

	<div class="InfoBlock">
		<div class="InfoTabLine">
			<div class="InfoTab1">
				<img class="InfoTab1Transport" src="images/LogoT8.svg" />
				<img class="InfoTab1Event" src="images/IconTrafic.svg" />
			</div>
			<div class="InfoTabs">
				<div class="InfoTab2">
					<img class="InfoTab2Transport" src="images/LogoT8.svg" />
					<img class="InfoTab2Event" src="images/IconTrafic.svg" />
				</div>
				<div class="InfoTab2">
					<img class="InfoTab2Transport" src="images/LogoT8.svg" />
					<img class="InfoTab2Event" src="images/IconWorking.svg" />
				</div>
				<div class="InfoTab2">
					<img class="InfoTab2Transport" src="images/LogoT8.svg" />
					<img class="InfoTab2Event" src="images/IconNightWorking.svg" />
				</div>
				<div class="InfoTab2">
					<img class="InfoTab2Transport" src="images/LogoT11.svg" />
					<img class="InfoTab2Event" src="images/IconWorking.svg" />
				</div>
				<div class="InfoTab2">
					<img class="InfoTab2Transport" src="images/LogoT11.svg" />
					<img class="InfoTab2Event" src="images/IconTrafic.svg" />
				</div>
				<div class="InfoTab2">
					<img class="InfoTab2Transport" src="images/LogoT11.svg" />
					<img class="InfoTab2Event" src="images/IconNightWorking.svg" />
				</div>
			</div>
		</div>
		<div class="InfoCurrentTime">--:--:--</div>
		<div class="InfoMessages">
			<div class="InfoMessagesLine1"></div>
			<div class="InfoMessagesLine2"></div>
			Signaler à nos agents tout objet abandonné ou situation inhabituelle
			<img class="LogoLMU" src="images/LogoLMU.png" />
			<img class="LogoIDFM" src="images/LogoIDFM.png" />
		</div>
	</div>
	 
</div> -->
