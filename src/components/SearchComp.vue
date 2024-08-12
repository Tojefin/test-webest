<script setup lang="ts">
defineProps(['input'])
defineEmits(['update:input'])
</script>

<template>
	<input class="search" type="text" :value="input" @input="$emit('update:input', $event.target.value)" />
</template>

<style>
.search {
	margin-top: 50px;
	border: 1px solid #ccc;
	padding: 10px 20px;
	font-size: 16px;
	line-height: 20px;
	border-radius: 8px;
}
</style>