<script lang="ts">
	import './layout.css';
	import icon from '$lib/assets/raffle-up-icon.svg';
	import '../app.css';
	import BottomNavBar from '$lib/components/BottomNavBar.svelte';
	let { children } = $props();
</script>

<svelte:head><link rel="icon" href={icon} /></svelte:head>
{@render children()}
<BottomNavBar/>
