<script>
	// Export props
	let { Values } = $props();
</script>

<div class="Values">
	{#if Values.length === 0}
		<div class="Value">-</div>
		<div class="ValuesSeparator"></div>
		<div class="Value">-</div>
	{:else}
		<div class="Value">{Values[0]}</div>
		<div class="ValuesSeparator"></div>
		<div class="Value">{Values[1] ?? '-'}</div>
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

	.Value {
		height: 100%;
		color: #f9c823;
		font-family: 'Bold';
		font-size: 100pt;
		display: flex;
		align-items: center;
		justify-content: center;
		width: calc(0.5 * (100% - 2.5px));
	}

	.ValuesSeparator {
		background-color: white;
		width: 2.5px;
		height: calc(100% - (2 * 25px));
	}
</style>
