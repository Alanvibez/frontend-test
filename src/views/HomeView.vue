<template>
  <main>
    <FormBuilder :config="formConfig.parent.items" />
  </main>
</template>

<script setup>
import formConfig from '../../form-config.json';
import FormBuilder from '../components/FormBuilder.vue'

</script>
