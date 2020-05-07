<template>
	<div id="app">
		<head>
			<meta name="viewport" content="width=device-width, initial-scale=1.0">
		</head>
		<div class="inputResultsContainer">
			<div class="inputs">
				<h3>Numbers</h3>
				<div v-for="(item, index) in items" :key="item.id">
					<span class="inputIndexHelper">{{ index + 1 + "." }}</span><input :ref="'input' + index" @click="focusToInput(index)" v-on:keyup.delete="safeRemoveInputField(index)" v-on:keyup.enter="addNewItem(index)" v-model.number="item.value" type="number" class="avgInput" />
				</div>
				<p v-bind:class="{'hint-away': items.length > 2}" class="hint">Press ↩️ to create a new row</p>
			</div>
			<div class="results">
				<Results v-bind:resultData="getAvg()" />
				<Future />
			</div>
		</div>
	</div>
</template>

<script>
import Results from './components/Results.vue';
import Future from './components/Future.vue';

export default {
	name: 'App',
	components: {
		Results,
		Future,
	},
	data() {
        return {
            items: [
				{"value": 123},
			],
        }
	},
	methods: {
		getAvg: function() {
			let sum = 0;
			let count = 0;
			for (let i = 0; i < this.items.length; i++) {
				if (typeof this.items[i].value === 'number') {
					sum += this.items[i].value;
					count++;
				}
			}

			// object structure
			// sample response below
			// {decimal: 1.25, numerator: 5, denominator: 4, success: true}

			if (count > 0) {
				return {decimal: (sum/count), numerator: sum, denominator: count, success: true}
			}
			else
				return ":(";
		},

		addNewItem: function(fromInputIndex) {
			let emptyIndex = -1;
			for (let i = 0; i < this.items.length; i++) {
				if (this.items[i].value === '') {
					emptyIndex = i;
					break;
				}
			}
			if (emptyIndex > -1) {
				// there are some empty inputs that we have, let's point the user to these (assuming they're focused on the last input)
				// if they're not focused on the last input, focus user on the next input (emulate tab)
				if (fromInputIndex === this.items.length - 1)
					this.focusToInput(emptyIndex);
				else {
					// user not focused on last input, so let's emulate tab and focus them to the next input
					this.focusToInput(fromInputIndex + 1);
				}
			}
			else {
				// no empty inputs exists, continue on
				if (fromInputIndex === this.items.length - 1) {
					// user is focused on the last input, so we can add another input
					this.items.push({"value": ''});
					this.$nextTick(() => {
						let index = this.items.length - 1;
						this.focusToInput(index);
					})
				}
				else {
					// user is not focused on last input, so we focus the next input
					this.$nextTick(() => {
						this.focusToInput(fromInputIndex + 1);
					})
				}
			}
		},

		focusToInput: function(toInputIndex, dontSelect) {
			if (dontSelect === undefined) 
				this.$refs["input" + (toInputIndex)][0].select();
			else if (dontSelect === true)
				this.$refs["input" + (toInputIndex)][0].focus();
			else
				this.$refs["input" + (toInputIndex)][0].select();
		},

		safeRemoveInputField: function(fromInputIndex) {
			// user pressed backspace, check to see if it's empty, if so, delete the input
			// condition: input must be blank, and must be the last input
			if (this.items[fromInputIndex].value.length < 1 && fromInputIndex > 0) {
				this.$nextTick(() => {
					this.items.splice(fromInputIndex, 1);
					this.focusToInput(fromInputIndex - 1, true);
				})
			}
		},



	}
}
</script>

<style>
html, body {border: 0; margin: 0; padding: 0;}
body {font: 14px "Lato", Arial, sans-serif; min-width: 100%; min-height: 100%; color: #666; background-color: #fafafa;}
.container{margin: 0 auto; max-width: 1200px;}
*{margin: 0; padding: 0; box-sizing: border-box;text-align: center;}
.row{float: left; width: 100%; padding: 20px 0 50px;}
h2{text-align: center; color: #2079df; font-size: 28px; float: left; width: 100%; margin: 30px 0; position: relative; line-height: 58px; font-weight: 400;}
h2:before{content: ""; position: absolute; left: 50%; bottom: 0; width: 100px; height: 2px; background-color: #2079df; margin-left: -50px;}

#app {
	margin: 25px;
}

.avgInput:focus {
	/*border: 1px solid #E8F5E9;
	border-bottom: 2px solid #43A047;*/
	box-shadow: 0px 2px 0px 0px #43A047;
}

.avgInput {
	/*border: 1px solid transparent;
	border-bottom: 2px solid #A5D6A7;*/
	box-shadow: 0px 1.5px 0px 0px #A5D6A7;
	transition: box-shadow .25s ease;
	padding: 10px;
	margin-top: 10px;
}

textarea, input {
	border:none;
    background-image:none;
    background-color:transparent;
    -webkit-box-shadow: none;
    -moz-box-shadow: none;
    box-shadow: none;
	outline: none;
	text-align: left;
}

.inputIndexHelper {
	color:#bfbfbf;
	min-width: 20px;
	display: inline-block;
}

.inputResultsContainer {
	display: flex;
	flex-flow: row wrap;
	justify-content: space-around;
	padding: 0;
	margin: 0;
}

.results {
	min-width: 50%;
	height: 100%;
	border-radius: 8px;
	background: #fff;
    box-shadow: 0 6px 10px rgba(0,0,0,.08), 0 0 6px rgba(0,0,0,.05);
	padding: 25px;
	text-align: center;
	margin: 10px;
	transition: all .5s ease;
}

.inputs div {
	border-radius: 8px;
    background: #fff;
    box-shadow: 0 6px 10px rgba(0,0,0,.08), 0 0 6px rgba(0,0,0,.05);
	padding: 0 10px 10px 10px;
	margin: 10px;
	animation: fadein .33s cubic-bezier(0.25, -0.25, 0.15, 1.5);
	z-index:99;
}

.inputs div:focus {
    background: #fff;
    box-shadow: 0 6px 10px rgba(0,0,0,.08), 0 0 6px rgba(0,0,0,.05);
	padding: 0 10px 10px 10px;
	margin: 10px;
	animation: fadein .5s;
}

.hint {
	color: #595959;
	opacity: 1;
	transition: opacity .15s ease;
}

.hint-away {
	transform: translateY(-65px);
	opacity: 0;
}

@keyframes fadein {
    0% {
		transform: translate(0, -50px);
        opacity:0;
    }
    100% {
		transform: translate(0, 0);
        opacity:1;
    }
}

</style>
