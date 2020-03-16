<template>
  <div class="input__container" @mouseenter="hovering = true" @mouseleave="hovering = false" :style="{ backgroundColor: inputBackground }">
    <input ref="input" :placeholder="placeholder" :type="type"
      :class="`${placeholderEffect} ${prefixIcon ? 'prefix' : ''}`"
      :style="{ color: inputColor, borderColor: borderColor }"
      @input="handleInput"
      @focus="handleFocus"
      @blur="handleBlur"
      @change="handleChange"
			v-on:keydown="validate"
    />

    <span class="input__prefix" v-if="prefixIcon">
      <i class="icon" v-if="prefixIcon" :class="prefixIcon" />
    </span>
  </div>
</template>

<script>
export default {
  name: 'input',
  data () {
    return {
      hovering: false,
			focused: false,
			validate: this.$validation.validate
    }
  },
  props: {
    value: [ String, Number ],
    type: { type: String, default: 'text' },
    placeholder: { type: String, default: '' },
    placeholderEffect: { type: String, default: 'light' },
    inputColor: { type: String, default: '#606266' },
    inputBackground: { type: String, default: '#FFFFFF' },
    inputBorderColor: { type: String, default: '#DCDFE6' },
    inputBorderColorHovering: { type: String, default: '#C0C4CC' },
    inputBorderColorFocused: { type: String, default: '#409EFF' },
    prefixIcon: String,
    suffixIcon: String
  },
  computed: {
		borderColor () {
			return (this.focused)
				? this.inputBorderColorFocused
				: this.hovering ? this.inputBorderColorHovering : this.inputBorderColor;
    }
  },
  methods: {
    getInput     ()      { this.$refs && this.$refs.input ? this.$refs.input : null },
    focus        ()      { this.$refs.input.focus() },
    blur         ()      { this.$refs.input.blur () },
    handleInput  (event) { this.$emit('input', event.target.value ) },
    handleChange (event) { this.$emit('change', event.target.value ) },
    handleFocus  (event) {
      this.focused = true;
      this.$emit('focus', event);
    },
    handleBlur (event) {
      this.focused = false;
      this.$emit('blur', event);
    }
  }
}
</script>

<style scoped lang="scss">
.input__container{
	position: relative;
}

input {
  font-size: $input-text--font-size;
  border: 1px solid rgba(0, 0, 0, 0.2);
	height: 30px;
	width: 200px;
  border-radius: 3px;
	margin-right: 20px;
	transition: border-color .2s ease;
	padding-left: $input-text--padding-left;

	&:hover,
	&:focus,
	&:active{
		outline: 0;
	}

	&.prefix{
		padding-left: 30px;
	}
}

.input__prefix{
  position: absolute;
  height: 100%;
  left: 5px;
  top: 0;
  text-align: center;
  color: #C0C4CC;
  transition: all 0.3s;
}

i.icon{
  height: 100%;
  width: 25px;
  transition: all 0.3s;
  line-height: 28px;
  display: inline-block;
  text-align: center;
  vertical-align: baseline;
  font-weight: 400;
}
i.icon:after {
  content: '';
  height: 100%;
  width: 0;
  display: inline-block;
  vertical-align: middle;
}

.vue-input::-ms-clear {
  display: none;
  width: 0;
  height: 0;
}
</style>
