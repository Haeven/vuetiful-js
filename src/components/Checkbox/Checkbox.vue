<template>
	<div class="checkbox__container" @click.stop="check">
		<div class="checkbox">
			<div :class="`checkbox__check flex-c-c ${checked}`">
				<i class="icon icon--check" v-show="checked"></i>
			</div>
		</div>
		<slot name="label"></slot>
	</div>
</template>

<script>
export default {
  name: "checkbox",
  data() {
    return {
			checked: ''
		}
  },
  components: {},
  methods: {
		check() {
			this.checked = this.checked ? '' : 'is-checked';
		}
	}
};
</script>

<style lang="scss">
.checkbox__container {
	display: flex;
	position: relative;

	.checkbox {
		position: absolute;
		top: 50%;
		-ms-transform: translateY(-50%);
		transform: translateY(-50%);
		margin: 0;
		box-sizing: border-box;
		height: $checkbox--dimensions;
		max-width: $checkbox--dimensions;
		overflow: hidden;
		-webkit-flex: 0 0 50px;
		-ms-flex: 0 0 50px;
		flex: 0 0 50px;
		border: none;
	}

	.checkbox__check {
		box-sizing: border-box;
		height: $checkbox--dimensions;
		width: $checkbox--dimensions;
		border: $checkbox--border-width solid $checkbox--border-color;
		border-radius: $checkbox--border-radius;
		cursor: pointer;
		overflow: hidden;
	}

	.checkbox__check:hover {
		border-color: $checkbox--color;
	}
	.checkbox__check.is-checked {
		border-color: $checkbox--color;
		background-color: $checkbox--color;
		color: #ffffff;
	}

	label {
		margin: 0 30px;
		position: absolute;
		top: 50%;
		-ms-transform: translateY(-50%);
		transform: translateY(-50%);
	}
}
</style>
