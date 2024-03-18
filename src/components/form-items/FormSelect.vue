<template>
  <div>
    <p>{{ label }}</p>
    <select
      v-bind="$attrs"
      @input="$emit('update:modelValue', $event.target.value)"
    >
      <option
        v-for="option in options"
        :key="option.value"
        :selected="option.selected"
        :value="option.value"
      >
        {{ option.text }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "FormSelect",
  props: {
    label: {
      type: String,
      default: "",
    },

    options: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped>
select {
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

select:focus {
  outline: none;
  border-color: #4caf50;
}
</style>
