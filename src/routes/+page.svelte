<script>
	import { onMount } from 'svelte';
	import toast, { Toaster } from 'svelte-french-toast';

	export let data;
	let scrollY = 0;

	/**
	 * @type {string[]}
	 * @description Array of toast messages to be displayed.
	 */
	let messages = [];

	/**
	 * @type {string[]}
	 * @description Array of colors for each post.
	 */
	let colors = [];

	/**
	 * @type {boolean[]}
	 * @description Array of boolean values indicating whether a post has been liked or not.
	 */
	let likes = [];

	// Function to generate a pastel or light hex color
	function getPastelColor() {
		// Generate a random value for each color component
		const getColorComponent = () => {
			// Pastel colors are generally in the higher range, so use 128-255 for R, G, B values
			const base = 128; // minimum value for pastel colors
			const range = 128; // maximum additional value for pastel colors
			return Math.floor(Math.random() * range + base)
				.toString(16)
				.padStart(2, '0');
		};

		// Generate each color component and concatenate them
		let color = '#';
		color += getColorComponent();
		color += getColorComponent();
		color += getColorComponent();

		return color;
	}

	onMount(() => {
		scrollY = 0;
		messages = data.toasts;
		colors = Array.from({ length: 1000 }, () => getPastelColor());
		likes = Array.from({ length: 1000 }, () => false);
	});

	let visibleIndex = -1;
	let lastLoggedIndex = 0; // Keep track of the last logged index

	// Function to get the index of the currently visible post
	function getVisiblePostIndex() {
		const posts = document.querySelectorAll('.post');
		let index = -1;
		const viewportHeight = window.innerHeight;

		posts.forEach((post, i) => {
			const rect = post.getBoundingClientRect();
			if (rect.top >= 0 && rect.top < viewportHeight) {
				index = i;
			}
		});

		return index;
	}

	/**
	 * Send a toast message based on the given index.
	 *
	 * @param {number} index - A positive number representing the index of the post.
	 * @throws {Error} Will throw an error if index is not a positive number.
	 */
	function logMessageForPost(index) {
		// Check if the current index is a multiple of 20
		if (index % 20 === 0) {
			// Calculate the message index (0-based) by dividing the post index by 20
			const messageIndex = Math.floor(index / 20) - 1; // -1 to adjust for 1-based index

			// Check if the message index is within bounds
			if (messageIndex >= 0 && messageIndex < messages.length) {
				// Log the message only if it is different from the last logged index
				if (index !== lastLoggedIndex) {
					toast(messages[messageIndex], {
						duration: 5000
					});
					lastLoggedIndex = index; // Update the last logged index
				}
			}
		}
	}

	// Handle scroll event
	function handleScroll() {
		visibleIndex = getVisiblePostIndex() + 1;
		logMessageForPost(visibleIndex);
	}
</script>

<svelte:head>
	<title>Doom Scroll - By CommitsOverCoffee</title>
	<meta name="description" content="Transitioning app for recovering doom scrollers." />
</svelte:head>

<p id="credits" class="p-2 m-2 my-8 text-center align-middle text-gray-400 text-sm">
	© 2022
	<a class="text-gray-800" href="https://github.com/commitsovercoffee/doom-scroll">doom-scroll</a>
	· Built by <a class="text-gray-800" href="https://commitsovercoffee.com">commitsovercoffee</a>
</p>

<svelte:window on:scroll={handleScroll} bind:scrollY />
<Toaster />

<div class="flex flex-col items-center m-2 p-2 gap-8">
	{#each colors as color, index}
		<div>
			<div
				class="post rounded-xl w-80 h-80 shadow-md {index % 2 == 0
					? 'active:rotate-2'
					: 'active:-rotate-3'} focus:scale-95 active:scale-95 transition-all duration-200 ease-in-out"
				style="background-color: {color};"
			></div>
			<div
				class=" flex flex-row justify-between items-center p-2 border-b-2 border-dashed border-gray-200 w-80"
			>
				<svg
					role="presentation"
					on:click={() => {
						likes[index] = true;
					}}
					xmlns="http://www.w3.org/2000/svg"
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					stroke="#E97777"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="lucide lucide-heart m-2 {likes[index] &&
						'fill-red-400'} active:scale-90 transition-all duration-200"
					><path
						d="M19 14c1.49-1.46 3-3.21 3-5.5A5.5 5.5 0 0 0 16.5 3c-1.76 0-3 .5-4.5 2-1.5-1.5-2.74-2-4.5-2A5.5 5.5 0 0 0 2 8.5c0 2.3 1.5 4.05 3 5.5l7 7Z"
					/></svg
				>

				<svg
					role="presentation"
					on:click={() => {
						// Copy the text inside the text field
						navigator.clipboard.writeText('doom-scroll.commitsovercoffee.com');
						toast.success('Text Copied!', {
							duration: 2000
						});
					}}
					xmlns="http://www.w3.org/2000/svg"
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					stroke="#295F98"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="m-2 active:scale-90 transition-all duration-200"
					><circle cx="18" cy="5" r="3" /><circle cx="6" cy="12" r="3" /><circle
						cx="18"
						cy="19"
						r="3"
					/><line x1="8.59" x2="15.42" y1="13.51" y2="17.49" /><line
						x1="15.41"
						x2="8.59"
						y1="6.51"
						y2="10.49"
					/></svg
				>
			</div>
		</div>
	{/each}
</div>
