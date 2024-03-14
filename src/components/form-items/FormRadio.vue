<template>
  <fieldset>
    <legend>{{ label }}</legend>

    <div v-for="option in options" :key="option.value">
      <input
        required
        type="radio"
        :name="name"
        :id="option.value"
        :value="option.value"
        :checked="option.selected"
      />
      <label :for="option.value">{{ option.text }}</label>
    </div>
  </fieldset>
</template>

<script>
export default {
  name: "FormRadio",

  props: {
    label: {
      type: String,
      default: ""
    },
    name: {
      type: String,
      default: ""
    },
    options: {
      type: Object,
      required: true
    }
  }
};
</script>

<style scoped>
input[type="radio"] {
  /* Hide the default radio button */
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  outline: none;
  border: none;
  background-color: transparent;
  margin-right: 0.5rem;
  position: relative;
  top: 2px;
}

input[type="radio"]::before {
  /* Create a custom radio button */
  content: "";
  display: inline-block;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  border: 2px solid #ccc;
  background-color: #fff;
  position: relative;
  top: -1px;
  margin-right: 0.5rem;
}

input[type="radio"]:checked::before {
  /* Style the custom radio button when checked */
  background-color: #007bff;
  border-color: #6375fe;
}

label {
  /* Style the label for the radio button */
  cursor: pointer;
}
</style>
