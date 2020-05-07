<template>
	<div>
		<h3>Calculated Average</h3>
		<br>
		<h2> {{ (resultData.numerator/resultData.denominator).toFixed(2) }} </h2>
		<br>
		<div class="left-align">
			<h3> Sum = {{ resultData.numerator }} </h3>
			<h3> Count = {{ resultData.denominator }} </h3>
		</div>
	</div>
</template>

<script>
export default {
	name: 'Results',
	components: {
	},
	data() {
        return {
        }
	},
	methods: {
	},
	props: ["resultData"]
}
</script>

<style>

</style>