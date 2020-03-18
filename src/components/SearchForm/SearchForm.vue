<template>
		<div class="search-form">
				<h2>Search Parameters:</h2>
				<div class="search-form__container">
						<div v-for="field in formFields" v-bind:key="field.label" class="search-form__field">
								<label>{{ field.label }}</label>
								<search-input v-if="field.type !== 'dropdown'" :alt="oFormFields[field.label]" v-model="oFormFields[field.label]" :type="field.type"></search-input>
								<div v-else class="select__container">
										<i class="icon --down"></i>
										<select>
												<option v-for="item in field.items" v-bind:key="item" :value="item">{{ item }}</option>
										</select>
								</div>
						</div>
				</div>

				<search-checkbox>
					<label slot="label">{{ checkboxText }}</label>
				</search-checkbox>

				<div class="btn__container">
						<button class="btn --primary" v-on:click="searchFunc(oFormFields)">Search</button>
						<button class="btn --secondary"><span>Clear Search Fields</span></button>
				</div>
		</div>
</template>

<script>
import Input from '../Input/Input.vue';
import Checkbox from '../Checkbox/Checkbox.vue';

export default {
	name: 'search-form',
	data() {
		return {
			oFormFields: this.getFormFields()
		};
	},
	components: { 'search-input': Input, 'search-checkbox': Checkbox },
	methods: {
		getFormFields() {
			return this.formFields.reduce((agg, cur) => { agg[cur.label] = ''; return agg; }, {});
		}
	},
	props: {
		formFields: Array,
		searchFunc: Function,
		enableCheckbox: Boolean,
		checkboxText: String,
	}
};
</script>

<style lang="scss">
@import './SearchForm.scss'
</style>
