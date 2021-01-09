<script>
	let windowWidth;
	let windowHeight;
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<iframe
	title="script-preview"
	src="https://yostandsharp.s3.us-east-2.amazonaws.com/Macs+Dad+Comes+Out+-+Always+Sunny+Spec+-+Yost%2BSharp.pdf"
	width={windowWidth}
	height={windowHeight}
/>
