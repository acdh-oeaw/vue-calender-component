<script setup lang="ts">
const props = defineProps<{
	month: number;
	year: number;
}>();

const date = new Date(props.year, props.month);
const nDays = new Date(props.year, props.month, 0).getDate();
</script>

<template>
	<div class="grid grid-cols-7">
		<span class="col-span-7 text-center">{{
			date.toLocaleString("default", { month: "long" })
		}}</span>
		<span>So</span>
		<span>Mo</span>
		<span>Di</span>
		<span>Mi</span>
		<span>Do</span>
		<span>Fr</span>
		<span>Sa</span>
		<span v-for="blank in date.getDay()" :key="blank"></span>
		<span v-for="day in nDays" :key="day">
			{{ day }}
		</span>
	</div>
</template>
