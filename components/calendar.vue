<script setup lang="ts">
export interface DateObject {
	name: string;
	startDate: string;
	endDate?: string;
	tageszaehler?: number;
	link?: string;
	color?: string;
	properties?: object;
	currDate?: string;
}

const props = defineProps<{
	startYear?: number;
	endYear?: number;
	selectedYear?: number;
	data: Array<DateObject>;
}>();
defineEmits(["dateClick"]);

const range = computed(() => {
	const start =
		props.startYear ??
		props.data.map((date) => new Date(date.startDate).getFullYear()).sort()[0] ??
		1900;
	const end =
		props.endYear ??
		props.data
			.map((date) => new Date(date.startDate).getFullYear())
			.sort()
			.reverse()[0] ??
		2000;
	return { start, end };
});

const dataMap = new Map();

const dataWithDuplicates: Array<DateObject> = [];

props.data.forEach((date) => {
	if (date.endDate) {
		const endDate = new Date(date.endDate);

		// fill gap of multi day events with dates
		for (
			const currDate = new Date(date.startDate);
			currDate.toISOString() <= endDate.toISOString();
			currDate.setDate(currDate.getDate() + 1)
		) {
			dataWithDuplicates.push({
				...date,
				currDate: currDate.toISOString().split("T")[0],
			});
		}
	} else {
		dataWithDuplicates.push(date);
	}
});

dataWithDuplicates.forEach((date) => {
	console.log(date);

	const isoDate = new Date(date.currDate ?? date.startDate);
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

const selected = ref(props.selectedYear ?? range.value.end);
</script>

<template>
	<div class="flex flex-wrap">
		<div class="h-fit min-w-36 grow basis-1/4">
			<div class="text-center text-2xl">Jahr</div>
			<div class="flex flex-wrap justify-center">
				<button
					v-for="opt in range.end - range.start + 1"
					:key="opt"
					class="m-2 rounded border-2 p-2 transition hover:bg-slate-200"
					:class="{
						'border-transparent': opt - 1 + range.start !== selected,
						'border-gray-400': opt - 1 + range.start === selected,
						'bg-green-200': dataMap.has(opt - 1 + range.start),
					}"
					:value="opt - 1 + range.start"
					@click="selected = opt - 1 + range.start"
				>
					{{ opt - 1 + range.start }}
				</button>
			</div>
		</div>
		<div class="grow basis-3/4">
			<div class="text-center text-2xl">{{ selected }}</div>
			<Year
				:key="selected"
				:year="selected"
				:data="dataMap.get(selected)"
				@date-click="$emit('dateClick', $event)"
			/>
		</div>
	</div>
</template>
