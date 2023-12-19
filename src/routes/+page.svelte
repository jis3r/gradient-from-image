<script>
	let file = null;

	function handleFiles(event) {
		file = event.target.files[0] || event.dataTransfer.files[0];
		console.log(file);
	}

	function handleDrop(event) {
		event.preventDefault();
		file = event.dataTransfer.files[0];
		console.log(file);
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
				<input type="file" accept="image/*" id="fileInput" class="hidden" on:change={handleFiles} />
				<p class="text-white">Drop your image here or click to select</p>
			</div>
		</div>

		{#if file}
			<div class="text-white">
				<strong>Selected File:</strong>
				{file.name}
			</div>
		{/if}
	</div>
</main>
