<script lang="ts" setup>
import { ElDropdown, ElDropdownMenu, ElDropdownItem, type Placement } from 'element-plus';

import type { UserAction } from 'n8n-design-system/types';
import type { IconOrientation, IconSize } from 'n8n-design-system/types/icon';

import N8nIcon from '../N8nIcon';

const SIZE = ['mini', 'small', 'medium'] as const;
const THEME = ['default', 'dark'] as const;

interface ActionToggleProps {
	actions?: UserAction[];
	placement?: Placement;
	size?: (typeof SIZE)[number];
	iconSize?: IconSize;
	theme?: (typeof THEME)[number];
	iconOrientation?: IconOrientation;
}

defineOptions({ name: 'N8nActionToggle' });
withDefaults(defineProps<ActionToggleProps>(), {
	actions: () => [],
	placement: 'bottom',
	size: 'medium',
	theme: 'default',
	iconOrientation: 'vertical',
});

const emit = defineEmits<{
	action: [value: string];
	'visible-change': [value: boolean];
}>();
const onCommand = (value: string) => emit('action', value);
const onVisibleChange = (value: boolean) => emit('visible-change', value);
</script>

<template>
	<span :class="styles.container" data-test-id="action-toggle" @click.stop.prevent>
		<ElDropdown
			:placement="placement"
			:size="size"
			trigger="click"
			@command="onCommand"
			@visible-change="onVisibleChange"
		>
			<slot>
				<span :class="[styles.button, theme && styles[theme]]">
					<N8nIcon
						:icon="iconOrientation === 'horizontal' ? 'ellipsis-h' : 'ellipsis-v'"
						:size="iconSize"
					/>
				</span>
			</slot>

			<template #dropdown>
				<ElDropdownMenu data-test-id="action-toggle-dropdown">
					<ElDropdownItem
						v-for="action in actions"
						:key="action.value"
						:command="action.value"
						:disabled="action.disabled"
						:data-test-id="`action-${action.value}`"
					>
						{{ action.label }}
						<div :class="styles.iconContainer">
							<N8nIcon
								v-if="action.type === 'external-link'"
								icon="external-link-alt"
								size="xsmall"
								color="text-base"
							/>
						</div>
					</ElDropdownItem>
				</ElDropdownMenu>
			</template>
		</ElDropdown>
	</span>
</template>

<style lang="scss" module>
.container > :global(*) {
	line-height: 1;
}

.button {
	cursor: pointer;
	padding: var(--spacing-4xs);
	border-radius: var(--border-radius-base);

	&:hover {
		color: var(--color-primary);
		cursor: pointer;
	}

	&:focus {
		color: var(--color-primary);
	}
}

.dark {
	color: var(--color-text-dark);

	&:focus {
		background-color: var(--color-background-xlight);
	}
}

.iconContainer {
	display: inline;
}

:global(li:hover) .iconContainer :global(svg) {
	color: var(--color-primary-tint-1);
}
</style>