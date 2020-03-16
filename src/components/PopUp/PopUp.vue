<template>
    <div class="popup__container">
        <div class="popup">
            <h4>{{ header }}</h4>
            <p>{{ alertMessage }}</p>
            <div class="btn__container">
                <button :class="`btn ${leftBtnClass || 'btn --light --gray-fill'}`" :hidden="!leftButton">{{ leftButton }}</button>
                <button :class="`btn ${rightBtnClass || 'btn --primary --white-fill'}`" :hidden="!rightButton">{{ rightButton }}</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
	name: 'vuetiful-popup',
	components: {},
	methods: {},
	props: {
		header: String,
		alertMessage: String,
		leftButton: String,
		rightButton: String,
		leftBtnClass: String,
		rightBtnClass: String
	}
};
</script>

<style scoped lang="scss">
.popup__container {
  position: absolute;
  background-color: rgba(0, 0, 0, 0.6);
  top: 0;
  width: 100%;
  height: 100%;

  .popup {
    margin: auto;
    padding: 30px;
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%,-50%);
    height: $popup--height;
    width: $popup--width;
    background-color: white;

    h4 {
      text-align: left;
      border-bottom: 0.5px solid rgba(0, 0, 0, 0.3);
      padding-bottom: 10px;
      margin-bottom: 0px;
    }

    p {
      border-bottom: 0.5px solid rgba(0, 0, 0, 0.3);
      padding: 30px;
    }

    .btn__container {
			padding-bottom: 30px;

      & button {
        border-width: $popup__btn-border--width;
        padding-left: $popup__btn--padding-left;
        padding-right: $popup__btn--padding-right;
        font-weight: bold;

        &:nth-of-type(1) {
          float: left !important;
        }

        &:nth-of-type(2) {
					margin-left: auto;
        }
      }
    }
  }
}
</style>