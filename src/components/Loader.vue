<template>
  <div class="wrapper">
    <div class="spinner"></div>
    <p><slot /></p>
  </div>
</template>

<script>
export default {
  name: 'Loader'
};
</script>

<style src="../style/loader.css"></style>