<script setup lang="ts">
import { Popover, PopoverButton, PopoverPanel } from "@headlessui/vue";
import { ChevronRight } from "lucide-vue-next";

import type { DateObject } from "./calendar.vue";

const props = defineProps<{
	month: number;
	year: number;
	data?: Map<number, Map<number, DateObject>>;
	firstDay?: number;
}>();

defineEmits(["dateClick"]);

const date = new Date(props.year, props.month);
const nDays = new Date(props.year, props.month + 1, 0).getDate();

function eventArray(day: number): Array<DateObject> {
	return props.data?.has(day)
		? Array.from(props.data.get(day), ([, value]: [number, DateObject]) => value).sort(
				(a, b) => (a.tageszaehler ?? 0) - (b.tageszaehler ?? 0),
			)
		: [];
}

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
		<Popover v-for="day in nDays" :key="day" class="relative -mx-0.5 h-[30px]">
			<div
				v-if="data && data.get(day)"
				class="absolute inset-0 -z-10 overflow-hidden"
				:style="{
					opacity: 1 - 1 / (data.get(day).size + 1),
					backgroundColor: eventArray(day)[0].color ?? 'red',
				}"
				:class="{
					'mx-0.5 rounded-lg': !eventArray(day).some((element) => element.currDate),
					'w-full': eventArray(day).some((element) => element.currDate),
					'rounded-l-lg': eventArray(day).some((element) => element.currDate === element.startDate),
					'rounded-r-lg': eventArray(day).some((element) => element.currDate === element.endDate),
				}"
			/>
			<PopoverButton
				as="button"
				class="relative flex h-full w-full cursor-pointer items-center justify-center py-0.5 transition hover:bg-slate-200"
				:class="{
					'mx-0.5 rounded-lg': !eventArray(day).some((element) => element.currDate),
					'w-full': eventArray(day).some((element) => element.currDate),
					'rounded-l-lg': eventArray(day).some((element) => element.currDate === element.startDate),
					'rounded-r-lg': eventArray(day).some((element) => element.currDate === element.endDate),
				}"
				@click="
					$emit('dateClick', {
						date: `${date.getFullYear()}-${date.getMonth() + 1}-${day}`,
						data: eventArray(day),
						event: $event,
					})
				"
			>
				{{ day }}
			</PopoverButton>
			<PopoverPanel
				v-if="data?.has(day)"
				as="div"
				class="absolute right-0 z-10 w-max rounded border bg-white"
			>
				<div v-for="event in eventArray(day)" :key="String(event)">
					<component
						:is="event.link ? 'a' : 'div'"
						:href="event.link"
						class="flex items-center justify-between gap-2 border p-2 text-left"
						:class="event.link && 'transition hover:bg-slate-200 active:bg-slate-300'"
					>
						<div>
							<h2>{{ event.name }}</h2>
							<span>
								{{ event.startDate }}
								<template v-if="event.endDate">- {{ event.endDate }}</template>
							</span>
						</div>
						<ChevronRight v-if="event.link" class="h-5 w-5 shrink-0" />
					</component>
				</div>
			</PopoverPanel>
		</Popover>
	</div>
</template>
