<script>
	import { browser } from '$app/environment';
	import { onMount } from 'svelte';
	import jsBarcode from 'jsbarcode';
	import fileSaver from 'file-saver';

	let barWidth = 2,
		barHeight = 100,
		barMargin = 10;
	let textSize = 24,
		textMargin = 0;
	let showText = true;
	let codeType = 'CODE39';
	let code = '0015010005675420010135433033';
	let codes = '';
	let canvas = null;

	function genCode() {
		if (browser) {
			jsBarcode('#barcode', code, {
				format: codeType,
				width: barWidth,
				height: barHeight,
				margin: barMargin,
				fontSize: textSize,
				textMargin: textMargin,
				displayValue: showText,
			});
		}
	}

	function download() {
		canvas.toBlob(function (blob) {
			const name = `image-${code}.png`;
			console.log('downloading', name);
			fileSaver.saveAs(blob, name);
		});
	}

	async function generate() {
		let codeArr = codes.split('\n');
		for (const c of codeArr) {
			code = c;
			genCode();
			download();

			// NOTE: Required to make sure the browser does not mess up the file names
			// Ref: https://stackoverflow.com/a/39914235
			await new Promise((r) => setTimeout(r, 500));
		}
	}

	onMount(() => {
		genCode();
	});
</script>

<canvas bind:this={canvas} alt="" id="barcode" class="mb-2" />

<div class="flex flex-row gap-2 mb-2">
	<div>Barcode type</div>
	<select class="border border-1" bind:value={codeType} on:change={() => genCode()}>
		<option value="CODE128">CODE128 auto</option>
		<option value="CODE128A">CODE128 A</option>
		<option value="CODE128B">CODE128 B</option>
		<option value="CODE128C">CODE128 C</option>
		<option value="EAN13">EAN13</option>
		<option value="EAN8">EAN8</option>
		<option value="UPC">UPC</option>
		<option value="CODE39">CODE39</option>
		<option value="ITF14">ITF14</option>
		<option value="ITF">ITF</option>
		<option value="MSI">MSI</option>
		<option value="MSI10">MSI10</option>
		<option value="MSI11">MSI11</option>
		<option value="MSI1010">MSI1010</option>
		<option value="MSI1110">MSI1110</option>
		<option value="pharmacode">Pharmacode</option>
	</select>
</div>

<div class="flex flex-row gap-2 mb-2">
	<div>Use this input for single code</div>
	<input class="border px-1" type="text" bind:value={code} on:change={() => genCode()} />
	<button class="border px-1 bg-gray-300" on:click={() => download()}>Save</button>
</div>

<div class="mb-2 grid grid-flow-row grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-y-2">
	<!-- Bar width -->
	<div>
		<p>Bar Width: {barWidth}</p>
		<input id="bar-width" type="range" min="1" max="4" step="1" bind:value={barWidth} on:change={() => genCode()} />
	</div>
	<!-- Height -->
	<div>
		<p>Bar Height: {barHeight}</p>
		<input id="bar-height" type="range" min="10" max="150" step="5" bind:value={barHeight} on:change={() => genCode()} />
	</div>

	<!-- Margin -->
	<div>
		<p>Bar Margin: {barMargin}</p>
		<input id="bar-margin" type="range" min="0" max="25" step="1" bind:value={barMargin} on:change={() => genCode()} />
	</div>

	<!-- Show text -->
	<div>
		<p>Show text: {showText}</p>
		<input type="checkbox" name="" id="" bind:checked={showText} on:change={() => genCode()} />
	</div>
	<!-- Font size -->
	<div>
		<p>Font Size: {textSize}</p>
		<div class="col-md-7 col-xs-11 slider-container"><input id="bar-fontSize" type="range" min="8" max="36" step="1" bind:value={textSize} on:change={() => genCode()} /></div>
	</div>
	<!-- Text margin -->
	<div>
		<p>Text Margin: {textMargin}</p>
		<div class="col-md-7 col-xs-11 slider-container"><input id="bar-text-margin" type="range" min="-15" max="40" step="1" bind:value={textMargin} on:change={() => genCode()} /></div>
	</div>
</div>

<div class="mb-2">
	<p>Paste codes here for bulk generation, one code per line</p>
	<div class="border px-1 mb-2 bg-gray-300">
		<button class="w-full" on:click={() => generate()}>Generate</button>
	</div>
	<textarea class="border px-1 w-full" bind:value={codes} rows={10} />
</div>

<style>
</style>
