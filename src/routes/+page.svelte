<script>
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

	// Create an array of 100 random colors
	const colors = Array.from({ length: 1000 }, () => getPastelColor());
</script>

<div class="flex flex-col items-center m-2 p-2 gap-8">
	{#each colors as color}
		<div class="rounded-xl w-80 h-80 shadow-md" style="background-color: {color};"></div>
	{/each}
</div>
