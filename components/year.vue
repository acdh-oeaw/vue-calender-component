<script setup lang="ts">
import Month from "./month.vue";

defineProps<{ year: number }>();
</script>

<template>
	<div class="grid grid-cols-4 gap-4">
		<Month v-for="n in 12" :key="n" :year="year" :month="n - 1" />
	</div>
</template>
