<script>
	import { onMount } from 'svelte';
	import DeviceDetector from 'device-detector-js';
	import { format } from 'date-fns';

	let windowWidth;
	let windowHeight;
	let slugTime;
	let device;
	let sections;
	let currentSectionIndex = 0;
	let visibleSections = [];
	let intervalId;
	let cursor = true;

	function stringToHTML(str) {
		const parser = new DOMParser();
		const doc = parser.parseFromString(str, 'text/html');
		return doc.body;
	}

	function setSlugTime() {
		const now = new Date();
		const hours = now.getHours();
		if (hours > 19 || hours < 4) {
			slugTime = 'Night';
		} else {
			slugTime = 'Day';
		}
	}

	function setDevice(userAgent) {
		const deviceDetector = new DeviceDetector();
		device = deviceDetector.parse(userAgent);
		return `${
			['a', 'e', 'i', 'o', 'u'].includes(device.device.brand[0].toLowerCase())
				? 'an'
				: 'a'
		} ${device.device.brand} ${
			device.device.type === 'desktop' ? 'desktop computer' : device.device.type
		}`;
	}

	function generateContent() {
		const deviceToDisplay = setDevice(window.navigator.userAgent);
		const time = format(new Date(), 'h:mm a');

		sections = [
			{ type: 'slugline', content: 'ON SCREEN' },
			{
				type: 'action',
				content: `Text sprawls across a blank page on ${deviceToDisplay}. The time reads <b>${time}<b>.`,
			},

			// eyes reflecting on the screen. They are wide, intrigued. The eyes
			// us talking at same time and bickering about it
			// we're open to feedback and revisions, so you can change any text in this document. Go on. Try it.
		];
	}

	function runTypingAnimation() {
		// TODO https://stackoverflow.com/questions/1280263/changing-the-interval-of-setinterval-while-its-running
		// TODO pause at periods
		// TODO bold stuff after the section is typed out
		// TODO skip animation
		intervalId = setInterval(() => {
			if (!visibleSections.length) {
				visibleSections[currentSectionIndex] = {
					...sections[currentSectionIndex],
					visible: '',
				};
			}

			const currentSection = { ...visibleSections[currentSectionIndex] };

			if (!currentSection) {
				visibleSections[currentSectionIndex] = {
					...sections[currentSectionIndex],
					visible: '',
				};
			} else if (
				currentSection.content.length === currentSection.visible.length
			) {
				visibleSections[currentSectionIndex].active = false;
				currentSectionIndex++;
				visibleSections[currentSectionIndex] = {
					...sections[currentSectionIndex],
					visible: '',
				};
			} else {
				currentSection.active = true;
				currentSection.visible = currentSection.content.slice(
					0,
					currentSection.visible.length + 1
				);

				visibleSections[currentSectionIndex] = currentSection;
			}
		}, 75);
	}

	onMount(() => {
		setSlugTime();
		generateContent();
		runTypingAnimation();
	});

	// $: console.log({ wsections, currentSectionIndex, visibleSections });

	$: if (sections && currentSectionIndex >= sections.length) {
		clearInterval(intervalId);
	}
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<svelte:head>
	<title>Ryan Yost & Corey Sharp</title>
</svelte:head>

<div id="document">
	{#if device}
		{#each visibleSections as { type, visible, active }}
			<div class="{type} line {active && 'active'}" contenteditable="true">
				{@html visible}
				<span id="cursor" class={!active && 'inactive'} contenteditable="false"
					>|</span
				>
			</div>
		{/each}
	{/if}
</div>
å
<!--<iframe-->
<!--	src="https://yostandsharp.s3.us-east-2.amazonaws.com/Macs+Dad+Comes+Out+-+Always+Sunny+Spec+-+Yost%2BSharp.pdf"-->
<!--	width="{windowWidth}"-->
<!--	height="{windowHeight}"-->
<!-- TODO SKIP ANIMATION BUTTON -->

<!--/>-->
<style>
	#document {
		width: 8.5in;
		/*width: 100%;*/
		border: 1px solid red;
		min-height: 100vh;
		margin: auto;
		padding-top: 1in;
		padding-bottom: 1in;
		padding-left: 1.5in;
		padding-right: 1.5in;
		font-size: 16px;
		color: rgba(0, 0, 0, 1);
		/*font-weight: bold;*/
	}

	#cursor {
		margin-right: 20px;
		position: absolute;
		color: rgba(0, 0, 0, 0.75);
		animation: fadeinout 1s ease infinite;
		-webkit-animation: fadeinout 1s ease infinite;
		-moz-animation: fadeinout 1s ease infinite;
		-o-animation: fadeinout 1s ease infinite;
		-ms-animation: fadeinout 1s ease infinite;
	}

	.inactive {
		display: none !important;
	}

	.cursor-fade {
	}

	.active,
	.line:focus {
		background-color: rgba(60, 192, 245, 0.1);
		outline: none;
	}

	.line {
		min-height: 16px;
	}

	.line:focus {
		outline: none;
	}

	.slugline {
		font-weight: bold;
		text-transform: uppercase;
	}

	.slugline:not(:first-child) {
		margin-top: 32px;
	}

	.action {
		margin-top: 16px;
	}

	@keyframes fadeinout {
		0%,
		100% {
			opacity: 0;
		}
		50% {
			opacity: 1;
		}
	}

	@-moz-keyframes fadeinout {
		0%,
		100% {
			opacity: 0;
		}
		50% {
			opacity: 1;
		}
	}

	@-webkit-keyframes fadeinout {
		0%,
		100% {
			opacity: 0;
		}
		50% {
			opacity: 1;
		}
	}

	@-o-keyframes fadeinout {
		0%,
		100% {
			opacity: 0;
		}
		50% {
			opacity: 1;
		}
	}

	@-ms-keyframes fadeinout {
		0%,
		100% {
			opacity: 0;
		}
		50% {
			opacity: 1;
		}
	}
</style>
