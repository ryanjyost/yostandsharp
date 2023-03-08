<script>
	import { onMount } from 'svelte';
	import DeviceDetector from 'device-detector-js';
	import { format } from 'date-fns';
	import { scriptLinks } from '../helpers';

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
			device.device.type === 'desktop'
				? 'computer'
				: device.device.type || 'device'
		}`;

		if (!device.device.brand) {
			return `a ${type}.`;
		}

		const flipSideText = `The reader ROTATES their phone so that it's horizontal, easier to read.`;

		return `${
			['a', 'e', 'i', 'o', 'u'].includes(device.device.brand[0].toLowerCase())
				? 'an'
				: 'a'
		} ${device.device.brand || ''} ${type}. ${
			device.device.type === 'smartphone'
				? flipSideText
				: windowWidth < 600
				? flipSideText
				: ''
		}`;
	}

	function generateContent() {
		const deviceToDisplay = setDevice(window.navigator.userAgent);
		const time = format(new Date(), 'h:mm a');
		// const isBrowser =
		// 	device &&
		// 	device.client.type === 'browser' &&
		// 	device.device.type === 'desktop';

		const linkText = 'click to read';
		const linkStyle =
			'color: #551a8b; width: 100%; margin-bottom: 40px; display: block; text-align: left;';

		const links = Object.entries(scriptLinks).reduce((acc, [key]) => {
			acc[key] = `/scripts/${key}`;
			return acc;
		}, {});

		sections = [
			{ type: 'slugline', content: `INT. UNKNOWN - ${slugTime}` },
			{
				type: 'action',
				content: `A curious READER stares at the screen of ${deviceToDisplay} The time reads <b>${time}<b>.`,
			},
			{ type: 'slugline', content: `ON SCREEN` },
			{
				type: 'action',
				content: `The portfolio website for RYAN YOST & COREY SHARP.`,
			},
			{
				type: 'action',
				content: `Their contact info is prominent: <b>412.841.1697</b> and <b>yostandsharp@gmail.com</b>.`,
			},
			{ type: 'character', content: `Ryan (V.O.)` },
			{ type: 'dialogue', content: `Welcome to our-` },
			{ type: 'character', content: `Corey (V.O.)`, skipAnimation: true },
			{
				type: 'dialogue',
				content: `Just show them the scripts.`,
				skipAnimation: true,
			},
			{ type: 'character', content: `Ryan (V.O.)` },
			{
				type: 'dialogue',
				content: `Kinda had this whole big thing planned, though.`,
			},
			{ type: 'character', content: `Corey (V.O.)` },
			{
				type: 'dialogue',
				content: `People are busy.`,
			},
			{
				type: 'action',
				content: 'Beat. The reader wonders what the hell is going on.',
			},
			{ type: 'character', content: `Ryan (V.O.)` },
			{
				type: 'dialogue',
				content: `You're right. Good call.`,
			},
			{
				type: 'slugline',
				content: `MONTAGE - SCRIPTS BY RYAN YOST & COREY SHARP`,
			},
			{
				type: 'action',
				skipAnimation: true,
				content: `<b>SAVED</b> (Pilot) - After God finally gives up on Earth in 2020, a disillusioned but determined Jesus has one last chance to save humanity from itself and prove daddy wrong - by getting down and dirty in U.S. politics and setting his sights on the White House.`,
			},
			{
				type: 'action',
				disableEdit: true,
				skipAnimation: true,
				content: `<a href="${links['saved']}" target="_blank" style="${linkStyle}">${linkText}</a>`,
			},
			{
				type: 'action',
				skipAnimation: true,
				content: `<b>FRANZ MADE ME FAMOUS</b> (Feature) - A young, wannabe freedom fighter grapples with the nature of his work when he and his ragtag crew fumble their way through a life-defining mission to assassinate Franz Ferdinand, the royal dignitary of imperial Austria whose demise ignited the first world war.`,
			},
			{
				type: 'action',
				disableEdit: true,
				skipAnimation: true,
				content: `<a href="${links['franz-made-me-famous']}" target="_blank" style="${linkStyle}">${linkText}</a>`,
			},
			{
				type: 'action',
				skipAnimation: true,
				content: `<b>EVERYBODY'S GOT THEIR DEMONS</b> (Pilot) - An animated comedy series about a degenerate who grapples with her religious faith and opts to pursue nunhood to escape the torment of diabolically creative demons who will stop at nothing to make her and everyone else succumb to the dark side of life.
			`,
			},
			{
				type: 'action',
				disableEdit: true,
				skipAnimation: true,
				content: `<a href="${links['demons']}" target="_blank" style="${linkStyle}">${linkText}</a>`,
			},

			// { type: 'action', content: '', disableEdit: true },

			// { type: 'action', content: '', disableEdit: true },
			{
				type: 'action',
				skipAnimation: true,
				content: `<b>COPING</b> (Pilot) - When a rudderless twentysomething accidentally gets hired as an enforcer for a mysterious criminal organization, she’ll need her potent personality and weed dealer friend to excel in the new gig... and simply stay alive.`,
			},
			{
				type: 'action',
				disableEdit: true,
				skipAnimation: true,
				content: `<a href="${links['coping']}" target="_blank" style="${linkStyle}">${linkText}</a>`,
			},
			{
				type: 'action',
				skipAnimation: true,
				content: `<b>PEN ISLAND</b> (Short) - Where pens rule and pencils drool.`,
			},
			{
				type: 'action',
				disableEdit: true,
				skipAnimation: true,
				content: `<a href="${links['pen-island']}" target="_blank" style="${linkStyle}">${linkText}</a>`,
			},
			// { type: 'action', content: '', disableEdit: true },

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

		function calculateNextCounter(latestChar, currentSection) {
			const counters = {
				'.': 400,
				'-': 200,
				',': 300,
				default: 25,
			};

			if (currentSection.skipAnimation) return 0;

			function periodHandler() {
				if (currentSection.type === 'character') {
					return counters.default;
				}

				return counters[latestChar];
			}

			switch (latestChar) {
				case '.':
					return periodHandler();
				default:
					return counters[latestChar] || counters.default;
			}
		}

		let counter;
		let latestChar;

		const myFunction = function () {
			function addNewCharacter() {
				// update latest char so no how long to pause
				latestChar = currentSection.content.slice(
					0,
					currentSection.visible.length + 1
				)[currentSection.visible.length];

				// increment visible by 1
				currentSection.visible = currentSection.content.slice(
					0,
					currentSection.visible.length + 1
				);

				// update visible section
				visibleSections[currentSectionIndex] = currentSection;
			}

			// initiate first section
			if (!visibleSections.length) {
				visibleSections[currentSectionIndex] = {
					...sections[currentSectionIndex],
					visible: '',
				};
			}

			const currentSection = { ...visibleSections[currentSectionIndex] };
			// console.log({ currentSectionIndex, currentSection, visibleSections });

			// all sections are showing, so don't do anything
			if (sections.length === currentSectionIndex) {
				// console.log('yost 1');
				return;
			} else if (!currentSection || !currentSection.content) {
				// console.log('yost 2');
				// visibleSections[currentSectionIndex] = {
				// 	...sections[currentSectionIndex + 1],
				// 	visible: '',
				// };

				currentSectionIndex++;
			}
			// else if (currentSection.skipAnimation) {
			// 	// console.log('skip', { currentSection, currentSectionIndex });
			//
			// 	visibleSections[currentSectionIndex].visible =
			// 		visibleSections[currentSectionIndex].content;
			//
			// 	currentSectionIndex++;
			//
			// 	visibleSections[currentSectionIndex + 1] = {
			// 		...sections[currentSectionIndex + 1],
			// 		visible: '',
			// 	};
			// }
			//
			else if (
				currentSection.visible &&
				currentSection.content &&
				currentSection.content.length === currentSection.visible.length
			) {
				// console.log('all is visible', { currentSection, currentSectionIndex });
				currentSectionIndex++;
				visibleSections[currentSectionIndex] = {
					...sections[currentSectionIndex],
					visible: '',
				};
				// return setTimeout(myFunction, 500);
			} else if (!visibleSections[currentSectionIndex]) {
				// console.log('yost 4');
				visibleSections[currentSectionIndex] = {
					...sections[currentSectionIndex],
					visible: '',
				};

				currentSectionIndex++;
			} else {
				addNewCharacter();
			}

			//================================================
			// Calculate counter time
			counter = calculateNextCounter(latestChar, currentSection);

			// console.log({ counter, currentSectionIndex });

			if (currentSectionIndex < sections.length) {
				// console.log('run timeout');
				setTimeout(myFunction, counter);
			}
		};

		setTimeout(myFunction, counter);
	}

	onMount(() => {
		setSlugTime();
		generateContent();

		// if (!window.localStorage.getItem('skipAnimation')) {
		// 	runTypingAnimation();
		// 	window.localStorage.setItem('skipAnimation', 'true');
		// } else {
		currentSectionIndex = -1;
		visibleSections = [...sections].map((s) => {
			return { ...s, visible: s.content };
		});
		// }
	});
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<svelte:head>
	<title>Ryan Yost & Corey Sharp</title>
</svelte:head>

<div id="document">
	{#if device}
		{#each visibleSections as { type, visible, active, disableEdit }, i}
			<div
				class="{type} line {i === currentSectionIndex ? 'active' : ''}"
				contenteditable={!disableEdit}
			>
				{@html visible}
				<span
					id="cursor"
					class={i !== currentSectionIndex ? 'inactive' : ''}
					contenteditable="false">|</span
				>
			</div>
		{/each}
	{/if}
</div>

<nav>
	<ul>
		<li>
			<a href=".">home</a>
		</li>
		<li>
			<a href="/scripts/demons">Demons</a>
		</li>
		<li>
			<a href="/scripts/franz-made-me-famous">Franz Made Me Famous</a>
		</li>
		<li>
			<a href="/scripts/saved">Saved</a>
		</li>
		<li>
			<a href="/scripts/coping">Coping</a>
		</li>
		<li>
			<a href="/scripts/always-sunny-spec">Mac's Dad Comes Out</a>
		</li>
		<li>
			<a href="/scripts/pen-island">Pen Island</a>
		</li>
	</ul>
</nav>
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
	nav {
		opacity: 0;
	}

	a {
		font-size: 16px !important;
		width: 100%;
		text-align: right;
	}

	#document {
		width: 8.5in;
		max-width: 100%;
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
