<template>
    <nav
			v-on:click="toggleSideBar"
			@mouseenter="hovering = true"
			@mouseleave="hovering = false"
			class="box-shadow"
		>
      <i class="icon icon--nav"></i>
	</nav>
</template>

<script>
import navbarIcon from '../../assets/icons/navbar-menu-icon.svg';

export default {
	name: 'vuetiful-nav-bar',
	data: function() {
		return {
			navbarIcon,
			sideBarOpen: false,
			hovering: false
		};
	},
	methods: {
		toggleSideBar() {
			this.sideBarOpen = this.sideBarOpen ? false : true;
			this.$emit('toggleSideBar', this.sideBarOpen);
		}
	}
};
</script>

<style scoped lang="scss">
@import './NavBar.scss';
</style>
