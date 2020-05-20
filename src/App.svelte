<script>
	import moment from "moment";
	import Field from "./Field.svelte";

	const FORMAT_ISO = "YYYY-MM-DD";
	const FORMAT_FULL = "dddd " + FORMAT_ISO;
	const FORMAT_DAY = 'dddd';

  const stored = localStorage.getItem("birth_date");
  const initial = stored ? moment(stored) : moment();

  let year = initial.year();
  let month = initial.month() + 1;
	let day = initial.date();

	let now = moment();
	
	let when = 40;
	let whenText = 'Forty';
	const stones = [
		[20, 'Twinty'],
		[30, 'Thirty'],
		[40, 'Forty'],
		[50, 'Fifty'],
		[60, 'Sixty'],
		[70, 'Seventy'],
		[80, 'Eighty'],
		[90, 'Ninty'],
	];

  $: birth = moment([year, month - 1, day]);
	$: dayOfWeek = birth.isValid()
		? birth.format(FORMAT_DAY)
		: "Invalid date";
  $: localStorage.setItem(
		"birth_date",
		birth.format(FORMAT_ISO)
	);
  $: weeks = now.diff(birth, "weeks");
  $: target = birth.clone().add(when, "years");
  $: weeksUntilWhen = target.diff(now, "weeks");
</script>

<svelte:head>
  <title>Perspective</title>
</svelte:head>

<div class="wrapper p3 vgap2">

	<h2>When is your birthday?</h2>

	<div class="hor hgap2">
		<Field label="Day">
			<input
				class="huge black center"
				type="number"
				bind:value={day}
			/>
		</Field>

		<Field label="Month">
			<input
				class="huge black center"
				type="number"
				bind:value={month}
			/>
		</Field>
	</div>

	<div class="ver">
		<Field label="Year">
			<input
				class="swell huge black center"
				type="number"
				bind:value={year}
			/>
		</Field>
	</div>

	<p class="center input">
		<span class="big black">{dayOfWeek}</span>
		<span class="small">({weeks} weeks)</span>
	</p>

	<div class="py3 vgap2">
		<h2>Weeks until...?</h2>
		<select class="big black w100" bind:value={when}>
			{#each stones as stone}
				<option class="small" value={stone[0]}>{stone[1]}</option>
			{/each}
		</select>

		<p>It will be on <span class="bold">{target.format(FORMAT_FULL)}</span></p>
		<p>You have <span class="bold">{weeksUntilWhen}</span> weeks left</p>

		{#if weeksUntilWhen > 0 && weeksUntilWhen < 3000}
		<div class="hor wrap">
			{#each Array(Math.abs(weeksUntilWhen)) as _, i}
			<div
				class="box"
				class:blue={weeksUntilWhen > 0}
				class:red={weeksUntilWhen < 0}
			/>
			{/each}
		</div>
		{:else if weeksUntilWhen < 0}
			<div>Too late</div>
		{:else if weeksUntilWhen > 3000}
			<div>You have plenty of time</div>
		{/if}
	</div>
</div>

<style>
	.box {
		margin: 2px;
		width: 10px;
		height: 10px;
	}
	.blue {background-color: blue}
	.red {background-color: red}
</style>
