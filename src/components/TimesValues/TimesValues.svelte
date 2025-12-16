<script>
	export let Values;
</script>

<div class="Values">
	<div class="Value">{Values[0]}</div>
	{#each Values.slice(1) as ValuesItem}
		<div class="ValuesSeparator"></div>
		<div class="Value">{ValuesItem}</div>
	{/each}
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
