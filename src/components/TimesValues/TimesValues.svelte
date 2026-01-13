<script>
	// Export props
	let { Values, Destination } = $props();
</script>

<div class="Values">
	{#if Values.length === 0}
		<div class="BadValue"><span>.</span><span>.</span><span>.</span></div>
		<div class="ValuesSeparator"></div>
		<div class="BadValue"><span>.</span><span>.</span><span>.</span></div>
	{/if}
	{#if Values.length === 1}
		{#if Destination?.[0]}
			<div class="ValueDestination">
				<div class="Value">{Values[0]}</div>
				<div class="Destination">{Destination[0]}</div>
			</div>
		{:else}
			<div class="ValueDestination">
				<div class="Value">{Values[0]}</div>
			</div>
		{/if}
		<div class="ValuesSeparator"></div>
		<div class="BadValue"><span>.</span><span>.</span><span>.</span></div>
	{/if}
	{#if Values.length === 2}
		{#if Destination?.[0]}
			<div class="ValueDestination">
				<div class="Value ValueWithDestination">{Values[0]}</div>
				<div class="Destination">{Destination[0]}</div>
			</div>
		{:else}
			<div class="ValueDestination">
				<div class="Value">{Values[0]}</div>
			</div>
		{/if}
		<div class="ValuesSeparator"></div>
		{#if Destination?.[0]}
			<div class="ValueDestination">
				<div class="Value ValueWithDestination">{Values[1]}</div>
				<div class="Destination">{Destination[1]}</div>
			</div>
		{:else}
			<div class="ValueDestination">
				<div class="Value">{Values[1]}</div>
			</div>
		{/if}
	{/if}
</div>

<style>
	.Values {
		background-color: black;
		display: flex;
		align-items: center;
		margin: 25px;
		margin-left: 0;
		width: 400px;
		border-radius: 10px;
		height: calc(100% - (2 * 25px));
	}

	.ValueDestination {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 100%;
		flex-direction: column;
		width: calc(0.5 * (100% - 2.5px));
	}

	.Value {
		color: #f9c823;
		font-family: 'Bold';
		font-size: 100pt;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.ValueWithDestination {
		margin-top: -20px;
	}

	.Destination {
		color: white;
		margin-top: -5px;
		text-align: center;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: clip;
		width: calc(100% - (2 * 12.5px));
		font-size: 20pt;
	}

	.BadValue {
		height: 100%;
		display: flex;
		align-items: flex-start;
		justify-content: center;
		width: calc(0.5 * (100% - 2.5px));
		margin-top: -10px;
	}

	.BadValue span {
		color: #f9c823;
		font-family: 'Bold';
		font-size: 100pt;
		opacity: 1;
		animation: blink 5s infinite both;
	}

	.BadValue span:nth-child(1) {
		animation-delay: 0s;
	}

	.BadValue span:nth-child(2) {
		animation-delay: 1s;
	}

	.BadValue span:nth-child(3) {
		animation-delay: 2s;
	}

	@keyframes blink {
		0% {
			opacity: 1;
		}
		15% {
			opacity: 1;
		}
		25% {
			opacity: 0.25;
		}
		35% {
			opacity: 0.25;
		}
		45% {
			opacity: 1;
		}
	}

	.ValuesSeparator {
		background-color: white;
		width: 2.5px;
		height: calc(100% - (2 * 25px));
	}
</style>
