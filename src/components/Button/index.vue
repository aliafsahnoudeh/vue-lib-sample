<template>
  <button class="a-button">
    {{ text }}
  </button>
</template>

<script>
export default {
  name: 'Button',
  props: {
    text: {
      type: String,
      default: ''
    }
  }
}
</script>

<style scoped>
.a-button {
  font-weight: 500;
  outline: 0;
  border: 0;
  cursor: pointer;
  text-decoration: none;
  display: inline-block;
  text-align: center;
}
</style>
