<script>
	export let MainTransportType = 'LightTrain'; //  Metro | ExpressTrain | RegionalTrain | LightTrain | Cable | Bus
	export let MainTransportLine = '8';
	export let StationName = 'Saint-Denis - Porte de Paris';
</script>

<div class="Header">
	<div class="Icons">
		<img
			src="/assets/images/Logo{MainTransportType}.svg"
			alt="Logo af a light train"
			class="Icon"
		/>
		<img
			src="/assets/images/Logo{MainTransportType + MainTransportLine}.svg"
			alt="Logo of T8 light train"
			class="Icon"
		/>
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
		margin: 0 60px 0 60px;
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
