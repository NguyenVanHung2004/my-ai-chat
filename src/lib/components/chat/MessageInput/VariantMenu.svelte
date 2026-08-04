<script lang="ts">
	import { getContext } from 'svelte';

	import Dropdown from '$lib/components/common/Dropdown.svelte';
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

	const getCurrentVariant = (p: any) => {
		const effort = p?.reasoning_effort;
		if (typeof effort === 'string' && ['high', 'full', 'thinking'].includes(effort.toLowerCase())) {
			return variants[2];
		}
		if (typeof effort === 'string' && ['low', 'minimal'].includes(effort.toLowerCase())) {
			return variants[1];
		}
		return variants[0];
	};

	$: current = getCurrentVariant(params);

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
			<svelte:component this={current.icon} className="size-3.5 shrink-0" strokeWidth="1.75" />
			<span class="max-w-20 truncate">{current.label}</span>
		</button>
	</Tooltip>

	<div slot="content">
		<div
			class="glass-strong flex min-w-56 flex-col gap-0.5 rounded-xl p-1 text-gray-900 dark:text-white shadow-glass"
		>
			{#each variants as variant}
				<button
					type="button"
					class="flex w-full items-center gap-2 rounded-lg px-2 py-2 text-left text-[13px] font-normal cursor-pointer transition-colors hover:bg-gray-50/60 dark:hover:bg-white/[0.06]"
					on:click={() => selectVariant(variant)}
				>
					<svelte:component this={variant.icon} className="size-3.5 shrink-0" strokeWidth="1.75" />

					<div class="flex min-w-0 flex-1 flex-col">
						<div class="line-clamp-1 leading-4">{variant.label}</div>
						<div class="line-clamp-1 text-[11px] leading-4 text-gray-500 dark:text-gray-400">
							{variant.description}
						</div>
					</div>

					{#if current.id === variant.id}
						<Check className="size-3.5 shrink-0 text-violet-600 dark:text-violet-400" />
					{/if}
				</button>
			{/each}
		</div>
	</div>
</Dropdown>
