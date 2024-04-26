<script setup lang="ts">
import type { DateObject } from "./calendar.vue";

const props = defineProps<{
	month: number;
	year: number;
	data: Map<number, DateObject> | undefined;
	firstDay?: number;
}>();
defineEmits(["dateClick"]);
const date = new Date(props.year, props.month);
const nDays = new Date(props.year, props.month + 1, 0).getDate();

function actualMod(n: number, m: number) {
	return ((n % m) + m) % m;
}
</script>

<template>
	<div class="m-2 grid grid-cols-7 gap-1 rounded border p-2 text-center">
		<span class="col-span-7 mb-2 text-center">{{
			date.toLocaleString("default", { month: "long" })
		}}</span>
		<div class="col-span-7 grid grid-cols-7 gap-1">
			<span class="aspect-square" :style="{ order: actualMod(0 - (firstDay ?? 0), 7) }">So</span>
			<span class="aspect-square" :style="{ order: actualMod(1 - (firstDay ?? 0), 7) }">Mo</span>
			<span class="aspect-square" :style="{ order: actualMod(2 - (firstDay ?? 0), 7) }">Di</span>
			<span class="aspect-square" :style="{ order: actualMod(3 - (firstDay ?? 0), 7) }">Mi</span>
			<span class="aspect-square" :style="{ order: actualMod(4 - (firstDay ?? 0), 7) }">Do</span>
			<span class="aspect-square" :style="{ order: actualMod(5 - (firstDay ?? 0), 7) }">Fr</span>
			<span class="aspect-square" :style="{ order: actualMod(6 - (firstDay ?? 0), 7) }">Sa</span>
		</div>
		<span
			:class="date.getDay() === firstDay && 'hidden'"
			:style="{
				'grid-column': `span ${actualMod(date.getDay() - (firstDay ?? 0), 7)} / span ${actualMod(
					date.getDay() - (firstDay ?? 0),
					7,
				)}`,
			}"
		/>
		<button
			v-for="day in nDays"
			:key="day"
			class="flex aspect-square cursor-pointer items-center justify-center rounded-lg transition hover:bg-slate-200"
			:class="data?.get(day) && 'bg-green-200'"
			@click="
				$emit('dateClick', {
					date: `${date.getFullYear()}-${date.getMonth() + 1}-${day}`,
					data: data?.get(day) ? Array.from(data.get(day), ([, value]) => value) : null,
					event: $event,
				})
			"
		>
			{{ day }}
		</button>
	</div>
</template>
