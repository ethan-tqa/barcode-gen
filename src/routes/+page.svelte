<script>
	import { browser } from '$app/env';
	import { onMount } from 'svelte';
	import jsBarcode from 'jsbarcode';
	import fileSaver from 'file-saver';

	let barWidth = 2,
		barHeight = 100,
		barMargin = 10;
	let textSize = 24,
		textMargin = 0;
	let showText = true;
	let code = '0015010005675420010135433033';
	let codes = '';
	let canvas = null;

	function genCode() {
		if (browser) {
			jsBarcode('#barcode', code, {
				format: 'CODE39',
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
			fileSaver.saveAs(blob, 'image.png');
		});
	}

	function generate() {
		let codeArr = codes.split('\n');
		for (const c of codeArr) {
			code = c;
			genCode();
			download();
		}
	}

	onMount(() => {
		genCode();
	});
</script>

<canvas bind:this={canvas} alt="" id="barcode" />

<div class="row">
	<input type="text" bind:value={code} autofocus />
	<button on:click={() => download()}>Save</button>
</div>

<div class="row">
	<!-- Bar width -->
	<div class="col">
		<p>Bar Width: {barWidth}</p>
		<input id="bar-width" type="range" min="1" max="4" step="1" bind:value={barWidth} on:change={() => genCode()} />
	</div>
	<!-- Height -->
	<div class="col">
		<p>Bar Height: {barHeight}</p>
		<input id="bar-height" type="range" min="10" max="150" step="5" bind:value={barHeight} on:change={() => genCode()} />
	</div>

	<!-- Margin -->
	<div class="col">
		<p>Bar Margin: {barMargin}</p>
		<input id="bar-margin" type="range" min="0" max="25" step="1" bind:value={barMargin} on:change={() => genCode()} />
	</div>
</div>

<div class="row">
	<!-- Show text -->
	<div class="col">
		<p>Show text: {showText}</p>
		<input type="checkbox" name="" id="" bind:checked={showText} on:change={() => genCode()} />
	</div>
	<!-- Font size -->
	<div class="col">
		<p>Font Size: {textSize}</p>
		<div class="col-md-7 col-xs-11 slider-container"><input id="bar-fontSize" type="range" min="8" max="36" step="1" bind:value={textSize} on:change={() => genCode()} /></div>
	</div>
	<!-- Text margin -->
	<div class="col">
		<p>Text Margin: {textMargin}</p>
		<div class="col-md-7 col-xs-11 slider-container"><input id="bar-text-margin" type="range" min="-15" max="40" step="1" bind:value={textMargin} on:change={() => genCode()} /></div>
	</div>
</div>

<div>
	<p>Paste codes here for bulk generation, one code per line</p>
	<div>
		<button on:click={() => generate()}>Generate</button>
	</div>
	<textarea bind:value={codes} rows={10} />
</div>

<style>
	.row {
		column-count: 3;
	}
</style>
