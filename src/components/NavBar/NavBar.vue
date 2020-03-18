<template>
    <nav
			v-if="type === 'sidebar'"
			v-on:click="toggleSideBar"
			@mouseenter="hovering = true"
			@mouseleave="hovering = false"
			:class="{ 'box-shadow': shadow }"
		>
      <i class="icon icon--nav"></i>
	</nav>
	<nav
		v-else-if="type === 'navbar'"
		:class="{ 'box-shadow': shadow }"
	>
		<slot name="navbar-brand"></slot>
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
	},
	props: {
		type: String,
		shadow: Boolean
	}
};
</script>

<style scoped lang="scss">
@import './NavBar.scss';
</style>
