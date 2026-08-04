<script lang="ts">
	import { getContext } from 'svelte';

	import Dropdown from '$lib/components/common/Dropdown.svelte';
	import DropdownMenu from '$lib/components/common/DropdownMenu.svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';
	import Check from '$lib/components/icons/Check.svelte';
	import Sparkles from '$lib/components/icons/Sparkles.svelte';
	import Bolt from '$lib/components/icons/Bolt.svelte';
	import LightBulb from '$lib/components/icons/LightBulb.svelte';

	const i18n = getContext('i18n');

	export let params: any = {};
	export let onClose: Function = () => {};

	const variants = [
		{
			id: 'default',
			label: $i18n.t('Default'),
			description: $i18n.t('No reasoning override'),
			icon: Sparkles
		},
		{
			id: 'concise',
			label: $i18n.t('Concise'),
			description: $i18n.t('Low reasoning effort'),
			effort: 'low',
			icon: Bolt
		},
		{
			id: 'thinking',
			label: $i18n.t('Thinking'),
			description: $i18n.t('High reasoning effort'),
			effort: 'high',
			icon: LightBulb
		}
	];

	let show = false;

	const currentVariant = () => {
		const effort = params?.reasoning_effort;
		if (typeof effort === 'string' && ['high', 'full', 'thinking'].includes(effort.toLowerCase())) {
			return variants[2];
		}
		if (typeof effort === 'string' && ['low', 'minimal'].includes(effort.toLowerCase())) {
			return variants[1];
		}
		return variants[0];
	};

	const selectVariant = (variant) => {
		if (params) {
			if (variant.id === 'default') {
				delete params.reasoning_effort;
			} else {
				params.reasoning_effort = variant.effort;
			}
			params = params;
		}

		show = false;
		onClose();
	};
</script>

<Dropdown
	bind:show
	onOpenChange={(state) => {
		if (state === false) {
			onClose();
		}
	}}
>
	<Tooltip content={$i18n.t('Model Variant')} placement="top">
		<button
			type="button"
			id="variant-menu-button"
			class="group flex items-center gap-1.5 rounded-full px-2 py-1 text-[13px] font-normal text-gray-600 dark:text-gray-300 hover:bg-gray-100/70 dark:hover:bg-white/[0.08] dark:hover:text-gray-100 transition-colors shrink-0"
			aria-label={$i18n.t('Model Variant')}
		>
			{#if currentVariant().icon}
				<svelte:component this={currentVariant().icon} className="size-3.5" strokeWidth="1.75" />
			{/if}
			<span class="max-w-20 truncate">{currentVariant().label}</span>
		</button>
	</Tooltip>

	<div slot="content">
		<DropdownMenu className="min-w-56 max-h-72 overflow-y-auto scrollbar-thin">
			{#each variants as variant}
				<button
					type="button"
					class="flex w-full items-center justify-between gap-2 rounded-xl px-2 py-1.5 text-[13px] font-normal cursor-pointer hover:bg-gray-50/40 dark:hover:bg-white/[0.06]"
					on:click={() => selectVariant(variant)}
				>
					<div class="flex min-w-0 items-center gap-2">
						<svelte:component this={variant.icon} className="size-3.5" strokeWidth="1.75" />

						<div class="flex min-w-0 flex-col text-left">
							<div class="line-clamp-1">{variant.label}</div>
							<div class="line-clamp-1 text-[11px] text-gray-400 dark:text-gray-500">
								{variant.description}
							</div>
						</div>
					</div>

					{#if currentVariant().id === variant.id}
						<Check className="size-3.5 shrink-0 text-violet-600 dark:text-violet-400" />
					{/if}
				</button>
			{/each}
		</DropdownMenu>
	</div>
</Dropdown>
