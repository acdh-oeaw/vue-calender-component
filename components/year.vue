<script setup lang="ts">
import type { DateObject } from "./calendar.vue";
import Month from "./month.vue";

defineProps<{
	year: number;
	data: Map<number, Map<number, DateObject>> | undefined;
}>();
defineEmits(["dateClick"]);
</script>

<template>
	<div class="grid grid-cols-4 gap-4">
		<Month
			v-for="n in 12"
			:key="n"
			:year="year"
			:month="n - 1"
			:data="data ? data.get(n - 1) : undefined"
			@date-click="$emit('dateClick', $event)"
		/>
	</div>
</template>
