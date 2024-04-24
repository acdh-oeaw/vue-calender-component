<script setup lang="ts">
const props = defineProps<{
	min: number;
	max: number;
}>();

const selected = ref(props.max);
</script>

<template>
	<div class="grid grid-cols-[auto_1fr]">
		<div>
			<label for="selectYear">Select Year:</label>
			<select v-model="selected">
				<option v-for="opt in max - min" :key="opt" :value="opt - 1 + min">
					{{ opt - 1 + min }}
				</option>
			</select>
		</div>
		<Year :key="selected" :year="selected" />
	</div>
</template>
