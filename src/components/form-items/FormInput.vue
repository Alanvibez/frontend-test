<template>
  <div>
    <p>{{ label }}</p>
    <input v-bind="$attrs" :type="type" />
    <p v-if="error" class="error">{{ error }}</p>
  </div>
</template>

<script>
export default {
  name: "FormInput",

  props: {
    label: {
      type: String,
      default: ""
    },
    type: {
      type: String,
      default: "text"
    },
    error: {
      type: String,
      default: ""
    }
  }
};
</script>

<style scoped>
input {
  width: 100%;
  padding: 6px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  font-size: 16px;
  font-family: inherit;
  transition: 0.3s;
}

input:focus {
  outline: none;
  border-color: #4caf50;
}

label {
  font-weight: bold;
  display: block;
  margin-bottom: 4px;
}
</style>
