<template>
  <main>
    <FormBuilder :config="formConfig" />
  </main>
</template>

<script setup>
import formConfig from '../../form-config.json';
import FormBuilder from '../components/FormBuilder.vue'

</script>
