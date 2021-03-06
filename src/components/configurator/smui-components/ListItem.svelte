<script lang="ts" context="module">
	export interface ListItemProps {
		id: string;
		name: string;
		value: string;
		wrapFocus: boolean;
		ripple: boolean;
		disabled: boolean;
		readonly: boolean;
		selected: boolean;
		href: string;
		label: string;
		labelRow2: string;
		labelRow3: string;
		title: string;
		ariaLabel: string;
		leadingIcon: IconType;
		trailingIcon: IconType;
		clickableLeadingIcon: boolean;
		clickableTrailingIcon: boolean;
	}
</script>

<script lang="ts">
	import {
		Item,
		Content,
		ListRole,
		Icon,
		ListType,
		PrimaryText,
		SecondaryText,
	} from "@smui/core/list";
	import { Radio } from "@smui/core/radio";
	import { Checkbox } from "@smui/core/checkbox";
	import LeadingIcon from "src/components/configurator/smui-components/icons/LeadingIcon.svelte";
	import TrailingIcon from "src/components/configurator/smui-components/icons/TrailingIcon.svelte";
	import {
		getImgPlaceholderSrc,
		ImgPlaceholderParams,
	} from "src/functions/imgPlacehoder";
	import { IconType } from "./icons";

	export let component: any;
	export let contentComponent: any;
	export let primaryTextComponent: any;
	export let secondaryTextComponent: any;
	export let iconComponent: any;

	export let value: string = undefined;
	export let disabled: boolean;
	export let ripple: boolean;
	export let selected: boolean = undefined;
	export let ariaLabel: string;
	export let title: string;
	export let label: string;
	export let labelRow2: string;
	export let labelRow3: string;

	export let leadingIcon: IconType;
	export let trailingIcon: IconType;
	export let clickableLeadingIcon: boolean;
	export let clickableTrailingIcon: boolean;

	export let listRole: ListRole | "listbox" = undefined;
	export let listType: ListType;
	export let listItemsRows: number;

	let imageRes: ImgPlaceholderParams;
	$: switch (listType) {
		case "image":
			imageRes = { width: 56, height: 56 };
			break;
		case "avatar":
			imageRes = { width: 40, height: 40 };
			break;
		case "thumbnail":
			imageRes = { width: 40, height: 40 };
			break;
		case "video":
			imageRes = { width: 100, height: 56 };
			break;
	}
	let imageTxt: string;
	$: if (imageRes) imageTxt = `${imageRes.width}x${imageRes.height}`;
	let imageSrc: string;
	$: if (imageRes)
		imageSrc = getImgPlaceholderSrc({ ...imageRes, text: imageTxt });

	export function getImageSrc() {
		return imageSrc;
	}

	export function getImageTxt() {
		return imageTxt;
	}
</script>

<svelte:options immutable={true} />

<svelte:component
	this={component}
	bind:selected
	{value}
	{disabled}
	{ripple}
	{ariaLabel}
	{title}
	let:selected
	on:change>
	<svelte-fragment slot="leading">
		{#if listType === 'image' || listType === 'avatar' || listType === 'thumbnail' || listType === 'video'}
			<img alt={imageTxt} src={imageSrc} />
		{:else if listType === 'icon' || listType === 'textual'}
			<LeadingIcon
				type={leadingIcon}
				component={iconComponent}
				button={clickableLeadingIcon} />
		{:else if listRole === 'radiogroup'}
			<span>
				<Radio checked={selected} />
			</span>
		{:else if listRole === 'group'}
			<span>
				<Checkbox checked={selected} />
			</span>
		{/if}
	</svelte-fragment>
	{#if label}
		<svelte:component this={contentComponent}>
			{#if listItemsRows === 1}
				{label}
			{:else if listItemsRows > 1}
				<svelte:component this={primaryTextComponent}>{label}</svelte:component>
				<svelte:component this={secondaryTextComponent}>
					{labelRow2}
				</svelte:component>
			{/if}
			{#if listItemsRows === 3}
				<svelte:component this={secondaryTextComponent}>
					{labelRow3}
				</svelte:component>
			{/if}
		</svelte:component>
	{/if}
	<svelte-fragment slot="trailing">
		{#if trailingIcon}
			<TrailingIcon
				type={trailingIcon}
				component={iconComponent}
				button={clickableTrailingIcon} />
		{/if}
	</svelte-fragment>
</svelte:component>
