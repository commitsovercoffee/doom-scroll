<script>
	import toast, { Toaster } from 'svelte-french-toast';

	export let data;
	let messages = data.toasts;

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

	// Create an array of 1000 random colors
	const colors = Array.from({ length: 1000 }, () => getPastelColor());

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
						duration: 5000,
						position: 'bottom-center'
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
		console.log(visibleIndex);
	}
</script>

<svelte:window on:scroll={handleScroll} />
<Toaster />

<div class="flex flex-col items-center m-2 p-2 gap-8">
	{#each colors as color}
		<div class="post rounded-xl w-80 h-80 shadow-md" style="background-color: {color};"></div>
	{/each}
</div>
