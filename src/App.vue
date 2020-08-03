<template>
	<div id="app">
		<app-order-form />
	</div>
</template>

<script>
import OrderForm from './components/OrderForm.vue';

export default {
	name: 'App',
	components: {
		'app-order-form': OrderForm,
	},
};
</script>

<style>
#app {
	font-family: Lato, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}
</style>
