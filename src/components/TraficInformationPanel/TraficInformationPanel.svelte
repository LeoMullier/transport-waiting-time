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
</style>
