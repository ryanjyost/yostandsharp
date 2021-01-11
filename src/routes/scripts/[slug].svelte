<script>
	import { onMount } from 'svelte';
	import { goto } from '@sapper/app';
	import { scriptLinks } from '../../helpers';
	let windowWidth;
	let windowHeight;
	let link;

	onMount(() => {
		const { pathname } = window.location;
		const key = pathname.split('/')[2];

		link = scriptLinks[key];

		if (!link) {
			goto('/');
		}
	});
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<iframe
	id="pdfViewer"
	type="application/pdf"
	title="script-preview"
	src={link}
/>

<style>
	#pdfViewer {
		position: absolute;
		top: 0px;
		left: 0px;
		bottom: 0px;
		right: 0px;
		width: 100%;
		height: 100%;
		border: none;
		margin: 0;
		padding: 0;
		overflow: hidden;
		z-index: 999999;
	}
</style>
