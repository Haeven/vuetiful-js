<template>
	<div class="popup__container">
		<div class="alert-popup box-shadow">
			<img class="popup__img--alert" src="https://upload.wikimedia.org/wikipedia/commons/9/99/OOjs_UI_icon_alert-yellow.svg" />
			<p class="popup__message--alert">{{ alertMessage }}</p>
			<button class="btn popup__btn--alert">{{ topButton }}</button>
			<button class="btn popup__btn--alert btn --secondary">{{ bottomButton }}</button>
		</div>
	</div>
</template>

<script>
export default {
	name: 'vuetiful-alert-popup',
	components: {},
	methods: {},
	props: {
		alertMessage: String,
		topButton: String,
		bottomButton: String
	}
};
</script>

<style scoped lang="scss">
.alert-popup {
  margin: auto;
  padding: 30px;
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%,-50%);
  height: $alert-popup--height;
  width: $alert-popup--width;
	background-color: white;
	text-align: center;
}

.popup__img--alert {
  width: $alert-popup__img--width;
  margin: 20px;
}

.popup__message--alert {
  color: $alert-popup--color;
  width: 100%;
}

.popup__btn--alert {
  width: $alert-popup__btn--width;
  background-color: $alert-popup--background-color;
  margin-top: $alert-popup__btn--margin-top;
  color: $alert-popup__btn--color;
  font-weight: bold;

  &:hover {
    background-color: $alert-popup--active-color;
    color: white;
  }
}
</style>
