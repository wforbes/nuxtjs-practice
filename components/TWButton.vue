<template>
	<input
		type="button"
		:value="value"
		@click="$emit('click')"
		:disabled="disabled" 
		class="inline-block px-5 py-2 mx-auto text-white bg-blue-600 rounded-full hover:bg-blue-700 md:mx-0 disabled:opacity-30"
	/>
</template>
<script setup>

const props = defineProps({
	value: String,
	disabled: Boolean
})

const emit = defineEmits(['click'])

</script>