<script>
	let fileInput;
	let image = null;
	let imgColours = [];
	let backgrounds = [];

	$: {
		if (image && !image.type.match('image.*')) {
			image = null;
			alert('Please upload an image file');
		} else {
			console.log('getting average colour');
			getAverageColour();
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

	const getAverageColour = () => {
		if (!image) return;

		const canvas = document.createElement('canvas');
		const ctx = canvas.getContext('2d');
		const img = new Image();
		img.src = URL.createObjectURL(image);

		img.onload = () => {
			canvas.width = img.width;
			canvas.height = img.height;
			ctx.drawImage(img, 0, 0);

			const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
			const data = imageData.data;

			imgColours = getAllColours(data);
			console.log(imgColours);
			backgrounds = displayAllColours(imgColours);
		};
	};

	const getAllColours = (data) => {
		let result = [];
		let tolerance = 10;

		for (let i = 0; i < data.length; i += 4) {
			let r = data[i];
			let g = data[i + 1];
			let b = data[i + 2];

			let found = false;
			for (let j = 0; j < result.length; j++) {
				let r2 = result[j][0];
				let g2 = result[j][1];
				let b2 = result[j][2];

				if (
					r2 >= r - tolerance &&
					r2 <= r + tolerance &&
					g2 >= g - tolerance &&
					g2 <= g + tolerance &&
					b2 >= b - tolerance &&
					b2 <= b + tolerance
				) {
					found = true;
					result[j][3]++;
					result[j][0] = Math.floor((r2 + r) / 2);
					result[j][1] = Math.floor((g2 + g) / 2);
					result[j][2] = Math.floor((b2 + b) / 2);
					break;
				}
			}

			if (!found) {
				result.push([r, g, b, 1]);
			}
		}

		return result;
	};

	const displayAllColours = (colours) => {
		let allColours = [];
		for (let i = 0; i < colours.length; i++) {
			let style = `background-color: rgb(${colours[i][0]}, ${colours[i][1]}, ${colours[i][2]});`;
			allColours.push(style);
		}
		allColours = allColours.slice(0, 5);

		return allColours;
	};
</script>

<main class="bg-neutral-950 h-screen w-screen" on:dragover={handleDragOver} on:drop={handleDrop}>
	<div class="flex flex-col items-center justify-center h-full">
		<h1 class="text-4xl text-white">Gradient from Image</h1>
		<div class="p-4">
			<div
				class="border-dashed border border-neutral-100 rounded-lg p-6 text-center cursor-pointer"
				role="button"
				tabindex="0"
				on:click={() => fileInput.click()}
				on:keypress={handleKeyPress}
			>
				<input
					type="file"
					accept="image/*"
					id="fileInput"
					class="hidden"
					on:change={handleInput}
					bind:this={fileInput}
				/>
				<p class="text-white">Drop your image here or click to select</p>
			</div>
		</div>

		{#if image}
			<div class="text-white">
				<strong>Selected File:</strong>
				{image.name}
			</div>
		{/if}

		<div class="flex flex-row flex-wrap">
			{#each backgrounds as background}
				<div class="w-16 h-16 rounded" style={background}></div>
			{/each}
		</div>
	</div>
</main>
