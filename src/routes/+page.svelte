<script>
	let image = null;

	$: {
		if (image && !image.type.match('image.*')) {
			image = null;
			alert('Please upload an image file');
		}
	}

	function handleInput(event) {
		image = event.target.files[0] || event.dataTransfer.files[0];
	}

	function handleDrop(event) {
		event.preventDefault();
		image = event.dataTransfer.files[0];
	}

	function handleDragOver(event) {
		event.preventDefault();
	}

	function handleKeyPress(event) {
		if (event.key === 'Enter' || event.key === ' ') {
			fileInput.click();
		}
	}
</script>

<main class="bg-neutral-950 h-screen w-screen">
	<div class="flex flex-col items-center justify-center h-full">
		<h1 class="text-4xl text-white">Gradient from Image</h1>
		<div class="p-4">
			<div
				class="border-dashed border border-neutral-100 rounded-lg p-6 text-center cursor-pointer"
				role="button"
				tabindex="0"
				on:drop={handleDrop}
				on:dragover={handleDragOver}
				on:click={() => fileInput.click()}
				on:keypress={handleKeyPress}
			>
				<input type="file" accept="image/*" id="fileInput" class="hidden" on:change={handleInput} />
				<p class="text-white">Drop your image here or click to select</p>
			</div>
		</div>

		{#if image}
			<div class="text-white">
				<strong>Selected File:</strong>
				{image.name}
			</div>
		{/if}
	</div>
</main>
