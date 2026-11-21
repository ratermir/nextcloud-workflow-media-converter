<template>
	<div class="wmc-display-conversion-batch">
		<div class="wmc-display-conversion-batch__label">
			{{ conversionBatch.sourceFolder }} *.{{ conversionBatch.sourceExtension }} &#8594; {{ conversionBatch.postConversionOutputRuleMoveFolder }} *.{{ conversionBatch.outputExtension }}
		</div>
		<div class="wmc-display-conversion-batch__status-container">
			<div class="wmc-display-conversion-batch__status">
				<StatusBadge :status="conversionBatch.status" />
			</div>
			<div class="wmc-display-conversion-batch__progress">
				<NcProgressBar size="medium"
					:value="
						(conversionBatch.converted / conversionBatch.unconverted || 0) * 100
					"
					:error="['has-failures', 'failed'].includes(conversionBatch.status)" />
				<div v-if="conversionBatch.status !== 'queued'"
					class="wmc-display-conversion-batch__progress-labels">
					<small class="wmc-display-conversion-batch__progress-labels-converted">{{ t('workflow_media_converter', 'Converted') }}:
						{{ conversionBatch.converted }}</small>
					<small class="wmc-display-conversion-batch__progress-labels-total">{{ t('workflow_media_converter', 'Total') }}:
						{{ conversionBatch.unconverted }}</small>
				</div>
			</div>
			<div class="wmc-display-conversion-batch__delete-button">
				<NcActions>
					<NcActionButton icon="icon-delete" @click="$emit('remove')" />
				</NcActions>
			</div>
		</div>
		<details v-if="errors.length"
			class="wmc-display-conversion-batch__error-message">
			<summary>
				{{ n('workflow_media_converter', '%n error', '%n errors', errors.length) }}
			</summary>
			<pre v-for="(error, index) in errors" :key="index">{{ error }}</pre>
		</details>
	</div>
</template>

<script>
import NcActions from '@nextcloud/vue/dist/Components/NcActions.js'
import NcActionButton from '@nextcloud/vue/dist/Components/NcActionButton.js'
import NcProgressBar from '@nextcloud/vue/dist/Components/NcProgressBar.js'
import StatusBadge from './StatusBadge.vue'

export default {
	components: { NcActions, NcActionButton, StatusBadge, NcProgressBar },

	props: {
		conversionBatch: {
			required: true,
			type: Object,
		},
	},

	computed: {
		errors() {
			return Array.isArray(this.conversionBatch.errors) ? this.conversionBatch.errors : []
		},
	},
}
</script>

<style lang="scss">
.wmc-display-conversion-batch {
	position: relative;
	border-radius: 0.5em;

	&__error-message {
		margin-top: .5em;

		summary {
			cursor: pointer;
			color: var(--color-error-text, #ff4402);
		}

		pre {
			margin: .5em 0;
			padding: .5em;
			max-height: 20em;
			overflow: auto;
			white-space: pre-wrap;
			word-break: break-word;
			font-size: .85em;
			background-color: var(--color-background-dark);
			border-radius: var(--border-radius);
		}
	}

	&__status {
		display: flex;
		align-items: center;
		justify-content: center;

		&-container {
			display: grid;
			grid-template-columns: 10em 1fr 4em;
			grid-template-rows: 1fr;
			align-items: center;
		}
	}

	&__progress {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;

		&-labels {
			display: flex;
			flex-direction: row;
			width: 100%;

			&-converted {
				display: block;
			}

			&-total {
				display: block;
				margin-left: auto;
			}
		}
	}

	&__delete-button {
		margin-left: auto;
	}
}
</style>
