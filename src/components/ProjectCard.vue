<template>
	<div class="col-md-4 rounded mb-4">
		<div class="card shadow-sm h-100 p-4">
			<img :src="project.image" class="card-img-top" alt="">
			<div class="card-body p-0">
				<h3 class="my-3 card-title">{{project.title}}</h3>

				<p class="cart-text mb-0">{{project.description}}</p>
			</div>
		</div>
	</div>
</template>

<script setup>
	import {defineProps} from 'vue';
	
	defineProps({
		project: Object
	})
</script>