<template>
	<div class="flex flex-col justify-between h-full">
		<div>
			<div class="flex items-center justify-between">
				<div class="font-semibold mb-1">
					{{ __(label) }}
				</div>
				<Badge
					v-if="isDirty"
					:label="__('Not Saved')"
					variant="subtle"
					theme="orange"
				/>
			</div>
			<div class="text-xs text-gray-600">
				{{ __(description) }}
			</div>
		</div>
		<SettingFields :fields="fields" :data="data.data" />
		<div class="flex flex-row-reverse mt-auto">
			<Button variant="solid" :loading="saveSettings.loading" @click="update">
				{{ __('Update') }}
			</Button>
		</div>
	</div>
</template>
<script setup>
import { createResource, Button, Badge } from 'frappe-ui'
import SettingFields from '@/components/SettingFields.vue'
import { watch, ref } from 'vue'

const isDirty = ref(false)

const props = defineProps({
	fields: {
		type: Array,
		required: true,
	},
	data: {
		type: Object,
		required: true,
	},
	label: {
		type: String,
		required: true,
	},
	description: {
		type: String,
	},
})

const saveSettings = createResource({
	url: 'frappe.client.set_value',
	makeParams(values) {
		console.log(values)
		return {
			doctype: 'Website Settings',
			name: 'Website Settings',
			fieldname: values.fields,
		}
	},
})

const update = () => {
	let fieldsToSave = {}
	let imageFields = ['favicon', 'banner_image', 'footer_logo']
	props.fields.forEach((f) => {
		if (imageFields.includes(f.name)) {
			fieldsToSave[f.name] = f.value ? f.value.file_url : null
		} else {
			fieldsToSave[f.name] = f.value
		}
	})
	saveSettings.submit({
		fields: fieldsToSave,
	})
}

watch(props.data, (newData) => {
	console.log(newData)
	if (newData && !isDirty.value) {
		isDirty.value = true
	}
})
</script>
