<template>
		<div class="search-form">
				<h2>Search Parameters:</h2>
				<div class="search-form__container">
						<div v-for="field in formFields" v-bind:key="field.label" class="search-form__field">
								<label>{{ field.label }}</label>
								<search-input v-if="field.type !== 'dropdown'" :alt="oFormFields[field.label]" v-model="oFormFields[field.label]" :type="field.type"></search-input>
								<div v-else class="select__container">
										<i class="icon icon--down"></i>
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
.search-form {
	padding: $search-form--padding;
	margin: $search-form--margin;
	text-align: left;

  .search-form__container {
    height: fit-content;
    width: 100%;
    display: flex;
		flex-wrap: wrap;
		text-align: center;

		.search-form__field {
			display: grid;
			height: $search-form__field--height;
			width: $search-form__field--min-width;

			label {
				height: 0px;
			}

			input {
				min-width: 200px;
			}
		}
	}

	.checkbox__container {
		width: 300px;
		height: 30px;
		margin-bottom: 20px;
	}

	.btn__container {
		text-align: right;

		button {
			width: $search-form__btn--width;

			&:nth-of-type(2) {
				align-self: flex-end;
				margin-left: auto;
				margin-right: 35px;
			}
		}
	}

	@media all and (max-width: 760px) {
		width: $search-form--mobile-width !important;

		.search-form__container .search-form__field {
			min-width: 100% !important;

			input, .select__container {
				margin-right: 0px !important;
			}
		}

		.btn__container {
			button {
				height: $search-form__btn--mobile-height;
				min-width: $search-form__btn--mobile-width;
			}

			button:nth-of-type(2) {
				margin-left: 47px !important;
				content: "" !important;

				span {
					display: none;
				}

				&:after {
					content: "Clear";
				}
			}
		}
	}

	.input__container--checkbox {
		margin-bottom: 20px;
	}
}
</style>
