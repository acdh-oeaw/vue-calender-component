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
	<div class="flex flex-wrap items-stretch">
		<Month
			v-for="n in 12"
			:key="n"
			class="min-w-56 grow basis-[calc(25%-1rem)]"
			:year="year"
			:month="n - 1"
			:data="data ? data.get(n - 1) : undefined"
			:first-day="1"
			@date-click="$emit('dateClick', $event)"
		/>
	</div>
</template>
