<script>
  export let onFileSelected;

  function handleChange(event) {
    if (onFileSelected) {
      const file = event.target.files[0];
      onFileSelected(file);
    }
  }
</script>

<input type="file" accept="image/*" on:change={handleChange} />

<style>
  input {
    margin: 20px 0;
  }
</style>