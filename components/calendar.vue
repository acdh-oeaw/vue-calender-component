<script setup lang="ts">
export interface DateObject {
	name: string;
	startDate: string;
	endDate?: string;
	tageszaehler?: number;
	link?: string;
	properties?: object;
}

const props = defineProps<{
	min: number;
	max: number;
	data: Array<DateObject>;
}>();
defineEmits(["dateClick"]);
const dataMap = new Map();

props.data.forEach((date) => {
	const isoDate = new Date(date.startDate);
	const year = isoDate.getFullYear();
	const month = isoDate.getMonth();
	const day = isoDate.getDate();

	if (!dataMap.has(year)) dataMap.set(year, new Map());
	if (!dataMap.get(year).has(month)) dataMap.get(year).set(month, new Map());
	if (!dataMap.get(year).get(month).get(day)) dataMap.get(year).get(month).set(day, new Map());

	dataMap
		.get(year)
		.get(month)
		.get(day)
		.set(date.tageszaehler ?? 0, date);
});

const selected = ref(props.max);
</script>

<template>
	<div class="grid grid-cols-[1fr_4fr]">
		<div class="flex h-fit flex-wrap">
			<button
				v-for="opt in max - min + 1"
				:key="opt"
				class="m-2 rounded p-2 transition hover:bg-slate-200"
				:class="opt - 1 + min === selected && 'bg-slate-300'"
				:value="opt - 1 + min"
				@click="selected = opt - 1 + min"
			>
				{{ opt - 1 + min }}
			</button>
		</div>
		<Year
			:key="selected"
			:year="selected"
			:data="dataMap.get(selected)"
			@date-click="$emit('dateClick', $event)"
		/>
	</div>
</template>
