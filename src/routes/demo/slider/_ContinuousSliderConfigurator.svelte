<script lang="ts">
	import { ContinuousSlider } from "@smui/core/slider";
	import { FormField, Label } from "@smui/core/form-field";
	import {
		Configurator,
		generateSvelteCode,
		generateSvelteTagCode,
	} from "src/components/configurator";
	import { Checkbox } from "@smui/core/checkbox";
	import CommonSliderOptions from "./_CommonSliderOptions.svelte";

	let value: number;
	let name = "continuous-slider";
	let useAriaLabel: boolean;
	let useTitle: boolean;
	let disabled: boolean;
	let useLabel: boolean = true;

	let ariaLabel: string;
	$: ariaLabel = useAriaLabel ? "Label" : undefined;

	let title: string;
	$: title = useTitle ? "Title" : undefined;

	let min: number = 0;
	let max: number = 10;

	let svelteCode: string;
	let scssCode: string;

	$: svelteCode = generateSvelteCode({
		tag: "FormField",
		props: ["vertical"],
		content: `
			${useLabel ? `<Label>Label</Label>` : ""}
			${getSliderCode(min, max, name, disabled, title, ariaLabel)}
		`,
	});

	function getSliderCode(
		minValue: typeof min,
		maxValue: typeof max,
		nameValue: typeof name,
		disabledValue: typeof disabled,
		titleValue: typeof title,
		ariaLabelValue: typeof ariaLabel
	) {
		return generateSvelteTagCode({
			tag: "ContinuousSlider",
			props: [
				"bind:value",
				`min={${minValue}}`,
				`max={${maxValue}}`,
				`name="${nameValue}"`,
				[disabledValue, "disabled"],
				[titleValue, `title="${titleValue}"`],
				[ariaLabelValue, `ariaLabel="${ariaLabelValue}"`],
			],
			content: getContentCode(),
			indentFirstLine: false,
			indentSize: 3,
		});
	}

	function getContentCode() {
		return ``;
	}
</script>

<style lang="scss">
	.preview-container {
		height: 10em;
	}

	.options-sidebar {
		:global(.mdc-select) {
			width: 100%;
		}
	}
</style>

<svelte:options immutable={true} />

<Configurator {svelteCode} {scssCode}>
	<div slot="preview" class="preview-container">
		<div>
			<FormField vertical>
				{#if useLabel}
					<Label>Label</Label>
				{/if}
				<ContinuousSlider
					bind:value
					min={0}
					max={10}
					{name}
					{disabled}
					{ariaLabel}
					{title} />
			</FormField>
		</div>
	</div>
	<div slot="values">
		<div>
			value:
			{#if value != null && typeof value === 'string'}
				"{value}"
			{:else}{value}{/if}
		</div>
	</div>
	<div slot="optionsSidebar" class="options-sidebar">
		<CommonSliderOptions
			bind:min
			bind:max
			bind:disabled
			bind:useTitle
			bind:useAriaLabel
			bind:useLabel />
	</div>
</Configurator>
