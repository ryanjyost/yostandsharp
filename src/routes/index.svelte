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
		if (hours > 18 || hours < 4) {
			slugTime = 'Night';
		} else {
			slugTime = 'Day';
		}
	}

	function setDevice(userAgent) {
		const deviceDetector = new DeviceDetector();
		device = deviceDetector.parse(userAgent);
		const type = `${
			device.device.type === 'desktop' ? 'desktop computer' : device.device.type
		}`;

		if (!device.device.brand) {
			return `a ${type}.`;
		}

		const flipSideText = `The visitor ROTATES their phone so that it's horizontal, easier to read.`;

		return `${
			['a', 'e', 'i', 'o', 'u'].includes(device.device.brand[0].toLowerCase())
				? 'an'
				: 'a'
		} ${device.device.brand} ${type}. ${
			device.device.type === 'smartphone' ? flipSideText : ''
		}`;
	}

	function generateContent() {
		const deviceToDisplay = setDevice(window.navigator.userAgent);
		const time = format(new Date(), 'h:mm a');

		sections = [
			{ type: 'slugline', content: `INT. UNKNOWN - ${slugTime}` },
			{
				type: 'action',
				content: `A curious VISITOR stares at the screen of ${deviceToDisplay} The time reads <b>${time}<b>.`,
			},
			{ type: 'slugline', content: `ON SCREEN` },
			{
				type: 'action',
				content: `The website for RYAN YOST & COREY SHARP. It's simple yet sophisticated, like the screenwriting duo themselves.`,
			},
			{ type: 'character', content: `Ryan (V.O.)` },
			{ type: 'dialogue', content: `Welcome to our-` },
			{ type: 'character', content: `Corey (V.O.)` },
			{
				type: 'dialogue',
				content: `Just the scripts, bud.`,
			},
			{ type: 'character', content: `Ryan (V.O.)` },
			{
				type: 'dialogue',
				content: `Kinda had this whole thing planned...`,
			},
			{ type: 'character', content: `Corey (V.O.)` },
			{
				type: 'dialogue',
				content: `Short and sweet.`,
			},
			{ type: 'action', content: 'Beat. Awkward silence. It lingers.' },
			{ type: 'character', content: `Ryan (V.O.)` },
			{
				type: 'dialogue',
				content: `Yeah, you're right. Good call.`,
			},
			{
				type: 'slugline',
				content: `MONTAGE - SCRIPTS BY RYAN YOST & COREY SHARP`,
			},
			{
				type: 'action',
				content: `A) <b>FRANZ MADE ME FAMOUS</b> - A young, wannabe freedom fighter grapples with the nature of his work when he and his ragtag crew fumble their way through a life-defining mission to assassinate Franz Ferdinand, the royal dignitary of imperial Austria whose demise ignited the first world war.`,
			},
			{
				type: 'action',
				disableEdit: true,
				content: `feature / comedy / <a href="https://google.com" style="color: #0000EE; font-size: 16px;">click to read</a>`,
			},
			{ type: 'action', content: '', disableEdit: true },
			{
				type: 'action',
				content: `B) <b>SAVED</b> - After God finally gives up on Earth in 2020, a disillusioned but determined Jesus has one last chance to save humanity from itself and prove daddy wrong - by getting down and dirty in U.S. politics and setting his sights on the White House.`,
			},
			{
				type: 'action',
				disableEdit: true,
				content: `pilot / half hour comedy / <a href="https://google.com" style="color: #551a8b; font-size: 16px;">click to read</a>`,
			},
			{ type: 'action', content: '', disableEdit: true },
			{
				type: 'action',
				content: `C) <b>COPING</b> - When a rudderless twentysomething accidentally gets hired as an enforcer for a mysterious criminal organization, she’ll need her potent personality and weed dealer friend to excel in the new gig... and simply stay alive.`,
			},
			{
				type: 'action',
				disableEdit: true,
				content: `pilot / half hour comedy / <a href="https://google.com" style="color: #551a8b; font-size: 16px;">click to read</a>`,
			},
			{ type: 'action', content: '', disableEdit: true },
			{
				type: 'action',
				content: `D) <b>MAC'S DAD COMES OUT</b> - When Luther comes out as gay at his parole hearing, the gang can’t agree on whether he should stay in prison.
`,
			},
			{
				type: 'action',
				disableEdit: true,
				content: `spec / It's Always Sunny in Philadelphia /  <a href="https://drive.google.com/file/d/1UdV_O2MTEj6cMHxFk_0UtYumeXBBYMR1/view" style="color: #551a8b; font-size: 16px;">click to read</a>`,
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
		}, 40);
	}

	onMount(() => {
		setSlugTime();
		generateContent();

		if (!window.localStorage.getItem('skipAnimation')) {
			runTypingAnimation();
			window.localStorage.setItem('skipAnimation', 'true');
		} else {
			visibleSections = [...sections].map((s) => {
				return { ...s, visible: s.content };
			});
		}
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
		{#each visibleSections as { type, visible, active, disableEdit }}
			<div
				class="{type} line {active ? 'active' : ''}"
				contenteditable={!disableEdit}
			>
				{@html visible}
				<span
					id="cursor"
					class={!active ? 'inactive' : ''}
					contenteditable="false">|</span
				>
			</div>
		{/each}
	{/if}
</div>
<!--<iframe-->
<!--	src="https://yostandsharp.s3.us-east-2.amazonaws.com/Macs+Dad+Comes+Out+-+Always+Sunny+Spec+-+Yost%2BSharp.pdf"-->
<!--	width="{windowWidth}"-->
<!--	height="{windowHeight}"-->
<!-- TODO SKIP ANIMATION BUTTON -->

<!--{-->
<!--"Version": "2012-10-17",-->
<!--"Id": "http referer policy example",-->
<!--"Statement": [-->
<!--{-->
<!--"Sid": "Allow get requests originating from www.example.com and example.com.",-->
<!--"Effect": "Allow",-->
<!--"Principal": "*",-->
<!--"Action": "s3:GetObject",-->
<!--"Resource": "arn:aws:s3:::yostandsharp/*",-->
<!--"Condition": {-->
<!--"StringLike": {-->
<!--"aws:Referer": [-->
<!--"https://www.yostandsharp.com/*",-->
<!--"https://yostandsharp.com/*",-->
<!--"https://festive-snyder-7ff31f.netlify.app/*",-->
<!--"https://www.festive-snyder-7ff31f.netlify.app/*"-->
<!--]-->
<!--}-->
<!--}-->
<!--}-->
<!--]-->
<!--}-->

<!--/>-->
<style>
	#document {
		width: 8.5in;
		/*width: 100%;*/
		min-height: 100vh;
		margin: auto;
		padding-top: 1in;
		padding-bottom: 1in;
		padding-left: 1.5in;
		padding-right: 1in;
		font-size: 16px;
		/*color: rgba(0, 0, 0, 1);*/
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

	.link {
		color: red !important;
	}

	.link:visited {
		color: #551a8b !important;
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
		min-width: 100%;
	}

	.slugline:not(:first-child) {
		margin-top: 32px;
	}

	.character {
		text-transform: uppercase;
		margin-top: 16px;
		white-space: pre-wrap;
		margin-left: 2.2in;
	}

	.dialogue {
		white-space: pre-wrap;
		margin-left: 1.5in;
		margin-right: 1.5in;
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
