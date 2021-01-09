<script>
	import { onMount } from 'svelte';
	import { goto } from '@sapper/app';
	let windowWidth;
	let windowHeight;
	let link;

	onMount(() => {
		const { pathname } = window.location;
		const key = pathname.split('/')[2];

		const links = {
			coping:
				'https://yostandsharp.s3.us-east-2.amazonaws.com/COPING+by+Ryan+Yost+%26+Corey+Sharp.pdf',
			saved:
				'https://yostandsharp.s3.us-east-2.amazonaws.com/SAVED+by+Ryan+Yost+%26+Corey+Sharp.pdf',
			'always-sunny-spec':
				'https://yostandsharp.s3.us-east-2.amazonaws.com/Macs+Dad+Comes+Out+-+Always+Sunny+Spec+-+Yost%2BSharp.pdf',
			'franz-made-me-famous':
				'https://yostandsharp.s3.us-east-2.amazonaws.com/FRANZ+MADE+ME+FAMOUS+by+Ryan+Yost+%26+Corey+Sharp.pdf',
		};

		link = links[key];

		if (!link) {
			goto('/');
		}
	});
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<iframe
	title="script-preview"
	src={link}
	width={windowWidth}
	height={windowHeight}
/>
