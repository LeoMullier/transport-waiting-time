<script>
	export let MainTransportType = 'LightTrain'; //  Metro | ExpressTrain | RegionalTrain | LightTrain | Cable | Bus
	export let MainTransportLine = '8';
	export let StationName = 'Saint-Denis - Porte de Paris';
</script>

<div class="Header">
	<div class="Icons">
		<img
			src="/assets/images/LogoIDFM.svg"
			alt="Logo of Île de France Mobilité company."
			class="Icon"
		/>
		<img src="/assets/images/LogoStadium1.svg" alt="Logo of a stadium." class="Icon" />
	</div>
	<div class="StationName">{StationName}</div>
</div>

<style>
	.Header {
		background-color: white;
		width: 100%;
		height: 200px;
		display: flex;
		align-items: center;
	}

	.Icons {
		margin: 0 25px 0 50px;
	}

	.Icon {
		height: 150px;
	}

	.StationName {
		font-family: 'Bold';
		font-size: 80pt;
		color: #25303b;
	}
</style>
