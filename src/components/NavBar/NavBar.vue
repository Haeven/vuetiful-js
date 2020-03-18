<template>
    <nav
			v-if="type === 'sidebar'"
			v-on:click="toggleSideBar"
			@mouseenter="hovering = true"
			@mouseleave="hovering = false"
			:style="{ 'backgroundColor': color || '#013f76' }"
			:class="{ 'box-shadow': shadow }"
		>
      <i class="icon icon--nav"></i>
	</nav>
	<nav
		v-else-if="type === 'navbar'"
		:class="{ 'box-shadow': shadow }"
		:style="{ 'backgroundColor': color || '#013f76', height: height || defaultHeight, lineHeight: height || defaultHeight  }"
	>
		<slot></slot>
		<div
			v-for="navItem of items"
			:key="navItem.slug"
			class="navbar__item"
			:class="{ '--dropdown': true }"
			:style="{ height: height || defaultHeight, lineHeight: height || defaultHeight }"
		>
			<p>{{ navItem.label }}</p>
			<i class="icon --down"></i>
		</div>
	</nav>
</template>

<script>
import navbarIcon from '../../assets/icons/navbar-menu-icon.svg';
import styles from './NavBar.scss';

export default {
	name: 'vuetiful-nav-bar',
	data: function() {
		return {
			navbarIcon,
			sideBarOpen: false,
			hovering: false,
			defaultHeight: styles.navbarHeight
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
		color: String,
		height: String,
		shadow: Boolean,
		items: Array
	}
};
</script>

<style scoped lang="scss">
@import './NavBar.scss';
</style>
