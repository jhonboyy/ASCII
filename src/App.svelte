<script>
	import FileInput from "./components/FileInput.svelte";
	import AsciiPreview from "./components/AsciiPreview.svelte";
	import ImageProcessor from "./components/ImageProcessor.svelte";
  
	let selectedFile = null;
	let asciiArt = "";
  
	function handleFileSelected(file) {
	  selectedFile = file;
	}
  
	function handleAsciiGenerated(event) {
	  asciiArt = event.detail;
	}
  </script>
  
  <h1>Image to ASCII Converter with Color</h1>
  <FileInput onFileSelected={handleFileSelected} />
  <ImageProcessor file={selectedFile} on:asciiGenerated={handleAsciiGenerated} />
  <AsciiPreview {asciiArt} />
  
  <style>
	h1 {
	  color: white;
	  text-align: center;
	}
	body {
	  font-family: monospace;
	  text-align: center;
	  background-color: #000;
	  color: #fff;
	}
  </style>