<script>
	import { onMount } from 'svelte';
	import { goto } from '@sapper/app';
	import DeviceDetector from 'device-detector-js';
	import { scriptLinks } from '../../helpers';
	let windowWidth;
	let windowHeight;
	let link;

	onMount(() => {
		const { pathname } = window.location;
		const key = pathname.split('/')[2];

		const deviceDetector = new DeviceDetector();
		const device = deviceDetector.parse(window.navigator.userAgent);
		const isBrowser =
			device &&
			device.client.type === 'browser' &&
			device.device.type === 'desktop';

		const links = scriptLinks[key];

		link = isBrowser ? links['aws'] : links['gdrive'];

		if (!link) {
			goto('/');
		}
	});
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<div
	style="overflow: auto!important; -webkit-overflow-scrolling: touch!important;"
>
	<iframe
		id="pdfViewer"
		type="application/pdf"
		style="position: absolute; top: 0; left: 0;"
		src="{link}"
		width="100%"
		height="100%"
	/>
</div>

<style>
	/*#pdfViewer {*/
	/*	-webkit-overflow-scrolling: touch !important;*/
	/*}*/
</style>
