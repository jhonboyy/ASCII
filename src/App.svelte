<script>
	import FileInput from "./components/FileInput.svelte";
	import AsciiPreview from "./components/AsciiPreview.svelte";
	import ImageProcessor from "./components/ImageProcessor.svelte";

	let selectedFile = null;
	let asciiArt = "";

	function handleFileSelected(file) {
		selectedFile = file;
	}

	function handleAsciiGenerated(ascii) {
		asciiArt = ascii;
	}
</script>

<h1>Image to ASCII Converter with Color</h1>
<FileInput onFileSelected={handleFileSelected} />
<ImageProcessor file={selectedFile} onAsciiGenerated={handleAsciiGenerated} />
<AsciiPreview {asciiArt} />