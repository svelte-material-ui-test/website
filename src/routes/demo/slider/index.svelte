<script lang="ts">
	import DiscreteSliderConfigurator from "./_DiscreteSliderConfigurator.svelte";
	import ContinuousSliderConfigurator from "./_ContinuousSliderConfigurator.svelte";
	import DiscreteRangeSliderConfigurator from "./_DiscreteRangeSliderConfigurator.svelte";
	import { Tab, Label } from "@smui/core/tab";
	import { TabBar } from "@smui/core/tab-bar";
	import ContinuousRangeSliderConfigurator from "./_ContinuousRangeSliderConfigurator.svelte";
	import { Typography } from "@smui/core/typography";

	let currentTab:
		| "continuous-slider"
		| "continuous-range-slider"
		| "discrete-slider"
		| "discrete-range-slider" = "continuous-slider";
</script>

<svelte:head>
	<title>Slider - SMUI</title>
</svelte:head>

<section>
	<Typography variant="headline2">Slider</Typography>

	<TabBar bind:active={currentTab}>
		<Tab key="continuous-slider">
			<Label>Continuous slider</Label>
		</Tab>
		<Tab key="continuous-range-slider">
			<Label>Continuous range slider</Label>
		</Tab>
		<Tab key="discrete-slider">
			<Label>Discrete slider</Label>
		</Tab>
		<Tab key="discrete-range-slider">
			<Label>Discrete range slider</Label>
		</Tab>
	</TabBar>

	{#if currentTab === 'continuous-slider'}
		<ContinuousSliderConfigurator />
	{:else if currentTab === 'continuous-range-slider'}
		<ContinuousRangeSliderConfigurator />
	{:else if currentTab === 'discrete-slider'}
		<DiscreteSliderConfigurator />
	{:else if currentTab === 'discrete-range-slider'}
		<DiscreteRangeSliderConfigurator />
	{/if}
</section>
