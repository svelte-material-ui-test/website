<script lang="ts">
	import { afterUpdate, onMount } from "svelte";
	import CodeSelector from "./CodeSelector.svelte";
	import { source } from "common-tags";

	export let svelteScriptCode: string = undefined;
	export let svelteCode: string;
	export let scssCode: string;

	let extraCodeHeight = 0; //em;
	let codeElement: HTMLDivElement;
	let emInPx: number;
	const minCodeEm = 14;

	const allowedErrorMargin = 1;
	let minCodeHeight: number;

	onMount(() => {
		emInPx = parseFloat(getComputedStyle(codeElement?.parentElement).fontSize);
		minCodeHeight = emInPx * minCodeEm;
	});

	function handleWindowResize() {
		checkAndFixCodeHeight();
	}

	let checkAndFixCodeHeightTimeout: number;
	function checkAndFixCodeHeight() {
		clearTimeout(checkAndFixCodeHeightTimeout);

		if (codeElement.clientHeight < minCodeHeight + allowedErrorMargin) {
			checkAndFixCodeHeightTimeout = setTimeout(() => {
				const realCodeHeight =
					extraCodeHeight !== 0
						? codeElement.clientHeight - extraCodeHeight * emInPx
						: codeElement.clientHeight;

				if (realCodeHeight < minCodeHeight + allowedErrorMargin) {
					extraCodeHeight = (minCodeHeight - realCodeHeight) / emInPx;
				} else {
					extraCodeHeight = 0;
				}
			}, 20);
		}
	}

	afterUpdate(() => {
		if (codeElement.clientHeight < minCodeHeight && !extraCodeHeight) {
			checkAndFixCodeHeight();
		}
	});

	function getSvelteCode(...deps) {
		if (svelteScriptCode) {
			const res = source`
				${svelteScriptCode.trim()}

				${svelteCode.trim()}
			`;
			return res;
		} else {
			return source(svelteCode);
		}
	}
</script>

<style lang="scss">
	@use "src/styles/smui/_variables";
	$padding: 1em;
	$border: variables.$border-color 1px solid;

	.configurator {
		max-width: 80em;
		display: grid;
		grid-template:
			"preview options-sidebar" max-content
			"code options-sidebar" 1fr
			/ 1fr minmax(200px, min-content);
		white-space: normal;
		border: $border;
		max-height: calc(80vh + var(--extra-code-height) - 64px);
	}

	.configurator__preview {
		grid-area: preview;
		padding: $padding;
		background-color: #efefef;
		border-right: $border;
		display: flex;
		flex-direction: column;
		overflow: auto;
		max-height: 60vh;
		align-items: center;

		.values {
			margin-top: 1em;
			display: flex;
			align-self: flex-end;
		}
	}

	.code {
		grid-area: code;
		min-width: 0;
		min-height: 0;
		overflow: auto;
		background: #1e1e1e;
	}

	.options-sidebar {
		grid-area: options-sidebar;
		padding: $padding;
		overflow: auto;

		:global([slot="optionsSidebar"]) {
			display: grid;
			grid-template: auto / minmax(150px, min-content) minmax(
					150px,
					min-content
				);
			gap: 0.6em;
			white-space: nowrap;
			width: max-content;

			> :global(div) {
				display: flex;
				flex-direction: column;
				justify-content: center;
			}
		}
	}

	.preview-slot {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
	}
</style>

<svelte:options immutable={true} />
<svelte:window on:resize={() => handleWindowResize()} />

<div class="configurator" style="--extra-code-height: {extraCodeHeight}em;">
	<div class="configurator__preview">
		<slot name="preview" class="preview-slot" />
		{#if $$slots.values}
			<div class="values">
				<slot name="values" />
			</div>
		{/if}
	</div>
	<div class="options-sidebar">
		<slot name="optionsSidebar" />
	</div>
	<div class="code" bind:this={codeElement}>
		<CodeSelector
			svelte={getSvelteCode(svelteCode, svelteScriptCode)}
			scss={scssCode} />
	</div>
</div>
