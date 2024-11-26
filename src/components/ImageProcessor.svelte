<script>
    import { createEventDispatcher } from "svelte";
  
    const ASCII_CHARS = [' ', '.', ':', '-', '=', '+', '*', '#', '%', '@'];
    const dispatch = createEventDispatcher();
  
    export let file;
  
    $: if (file) {
      const reader = new FileReader();
      reader.onload = function (e) {
        const img = new Image();
        img.onload = () => convertToASCII(img);
        img.src = e.target.result;
      };
      reader.readAsDataURL(file);
    }
  
    function convertToASCII(image) {
      const canvas = document.createElement("canvas");
      const ctx = canvas.getContext("2d");
  
      // Ajusta la resolución
      const newWidth = 200;
      const aspectRatio = image.height / image.width;
      const characterAspectRatio = 0.5;
      const newHeight = Math.round(newWidth * aspectRatio * characterAspectRatio);
  
      canvas.width = newWidth;
      canvas.height = newHeight;
      ctx.drawImage(image, 0, 0, newWidth, newHeight);
  
      const imageData = ctx.getImageData(0, 0, newWidth, newHeight);
      const pixels = imageData.data;
  
      let asciiArt = "";
      for (let y = 0; y < newHeight; y++) {
        for (let x = 0; x < newWidth; x++) {
          const offset = (y * newWidth + x) * 4; // RGBA
          const r = pixels[offset];
          const g = pixels[offset + 1];
          const b = pixels[offset + 2];
  
          // Escala de grises
          const brightness = (r + g + b) / 3;
  
          // Carácter ASCII correspondiente
          const charIndex = Math.floor((brightness / 255) * (ASCII_CHARS.length - 1));
          const asciiChar = ASCII_CHARS[charIndex];
  
          asciiArt += `<span style="color: rgb(${r},${g},${b});">${asciiChar}</span>`;
        }
        asciiArt += "<br>";
      }
  
      // Envía el arte generado al componente padre
      dispatch("asciiGenerated", asciiArt);
    }
  </script>