<script>
    // Conjunto ampliado de caracteres ASCII ordenados por densidad
    const ASCII_CHARS = [' ', '.', ',', ':', ';', '+', '*', '?', '%', 'S', '#', '@'];

    export let file;
    export let onAsciiGenerated; // Callback para el resultado

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

        // Obtener la relación de aspecto de la imagen
        const imageAspectRatio = image.width / image.height;

        // Obtener la relación de aspecto de los caracteres
        const charAspectRatio = getCharAspectRatio();

        // Calcular la relación de aspecto efectiva
        const effectiveAspectRatio = imageAspectRatio / charAspectRatio;

        // Definir el tamaño máximo en caracteres (aumentado para mayor definición)
        const maxWidth = 300; // Antes era 200
        const maxHeight = 300; // Antes era 200

        let newWidth, newHeight;

        if (effectiveAspectRatio >= 1) {
            // Imagen es más ancha que alta
            newWidth = maxWidth;
            newHeight = Math.round(newWidth / effectiveAspectRatio);
        } else {
            // Imagen es más alta que ancha
            newHeight = maxHeight;
            newWidth = Math.round(newHeight * effectiveAspectRatio);
        }

        canvas.width = newWidth;
        canvas.height = newHeight;

        // Dibujar la imagen ajustada en el canvas
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

                // Convertir a escala de grises usando luminancia relativa
                const brightness = 0.2126 * r + 0.7152 * g + 0.0722 * b;

                // Carácter ASCII correspondiente
                const charIndex = Math.floor((brightness / 255) * (ASCII_CHARS.length - 1));

                const asciiChar = ASCII_CHARS[charIndex];

                // Añade el carácter con color RGB
                asciiArt += `<span style="color: rgb(${r}, ${g}, ${b});">${asciiChar}</span>`;
            }
            asciiArt += "<br>";
        }

        // Llamada al callback para devolver el arte generado
        if (onAsciiGenerated) {
            onAsciiGenerated(asciiArt);
        }
    }

    function getCharAspectRatio() {
        // Crear un elemento temporal para medir la relación ancho/alto de la fuente
        const span = document.createElement("span");
        span.style.fontFamily = "Courier New, monospace"; // Asegurar fuente monoespaciada
        span.style.fontSize = "16px";
        span.style.visibility = "hidden";
        span.textContent = "M"; // Carácter alto típico
        document.body.appendChild(span);

        const charWidth = span.offsetWidth;
        const charHeight = span.offsetHeight;

        document.body.removeChild(span);

        // Relación ancho:alto
        return charWidth / charHeight;
    }
</script>