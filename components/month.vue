<script setup lang="ts">
import type { DateObject } from "./calendar.vue";

const props = defineProps<{
	month: number;
	year: number;
	data: Map<number, DateObject> | undefined;
}>();
defineEmits(["dateClick"]);
const date = new Date(props.year, props.month);
const nDays = new Date(props.year, props.month + 1, 0).getDate();
</script>

<template>
	<div class="m-2 grid grid-cols-7 gap-1 rounded border p-2 text-center">
		<span class="col-span-7 mb-2 text-center">{{
			date.toLocaleString("default", { month: "long" })
		}}</span>
		<span>So</span>
		<span>Mo</span>
		<span>Di</span>
		<span>Mi</span>
		<span>Do</span>
		<span>Fr</span>
		<span>Sa</span>
		<span v-for="blank in date.getDay()" :key="blank" class="aspect-square"></span>
		<button
			v-for="day in nDays"
			:key="day"
			class="flex aspect-square cursor-pointer items-center justify-center rounded-lg transition hover:bg-slate-200"
			:class="data?.get(day) && 'bg-green-200'"
			@click="
				$emit('dateClick', {
					date: `${date.getFullYear()}-${date.getMonth() + 1}-${day}`,
					data: data?.get(day) ? Array.from(data.get(day)) : null,
				})
			"
		>
			{{ day }}
		</button>
	</div>
</template>
