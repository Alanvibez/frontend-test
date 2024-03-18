<template>
  <div class="form-builder">
    <form ref="formRef" @submit.prevent="onSubmit" >
      <div class="form-container">
        <div v-for="(form, key) in config" :key="key">
          <h1>{{ form.name }}</h1>
          <component
            v-for="(field, index) in form.items"
            :is="componentMap[field.type]"
            :key="index"
            :label="field.label"
            :name="`${key}-${field.name}`"
            v-bind="field.additional"
            :error="validationErrors[form.name]"
            :modelValue="formData[key][field.name]"
            @update:modelValue="formData[key][field.name] = $event"
          />
        </div>
      </div>
      <!-- Вариант без лишних :options undefind в пропсах для input -->
      <!-- <template v-for="item in config" :key="item.id">
        <component
          v-if="item.type === 'select' || item.type === 'radio'"
          :is="componentMap[item.type]"
          :label="item.label"
          :name="item.name"
          :options="item.additional?.options"
          v-model="formData[item.name]"
          :value="formData[item.name]"
          @input="formData[item.name] = $event.target.value"
        />
        <component
          v-else
          :is="componentMap[item.type]"
          :label="item.label"
          :name="item.name"
          v-model="formData[item.name]"
          :value="formData[item.name]"
          @input="formData[item.name] = $event.target.value"
        />
      </template> -->
      <div class="form-footer">
        <button type="reset" class="danger">Стереть</button>
        <button type="submit">Отправить</button>
      </div>
    </form>
  </div>
</template>

<script>
import FormInput from "@/components/form-items/FormInput.vue";
import FormSelect from "@/components/form-items/FormSelect.vue";
import FormRadio from "@/components/form-items/FormRadio.vue";
import FormPassword from "@/components/form-items/FormPassword.vue";

export default {
  name: "FormBuilder",
  props: {
    config: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      formData: {},
      validationErrors: {},
    };
  },
  created() {
    this.initFormData();
  },
  methods: {
    initFormData() {
      for (const key in this.config) {
        if (Object.hasOwnProperty.call(this.config, key)) {
          const form = this.config[key];
          this.formData[key] = {}; // Создаем объект для каждой формы

          // Заполняем каждую форму данными из конфига
          form.items.forEach((field) => {
            if (field.type === "select" || field.type === "radio") {
              // Если это поле select или radio, устанавливаем первый выбранный вариант
              this.formData[key][field.name] =
                field.additional?.options.find((option) => option.selected)
                  ?.value || "";
            } else {
              // В противном случае устанавливаем пустую строку
              this.formData[key][field.name] = "";
            }
          });
        }
      }
    },
    onSubmit() {
      fetch("https://api.example.com/endpoint", {
        method: "POST",
        body: JSON.stringify(this.formData),
      })
        .then((response) => {
          if (response.ok) {
            this.onReset();
          }
        })
        .catch((error) => {});
    },
    onReset(e) {
      this.$refs.formRef.reset();
    },
    validateForm() {
      this.validationErrors = {};
      let isValid = true;

      this.formData.forEach((item) => {
        item.forEach((item) => {
          const value = item.value;
          if (!value.trim() && item.required) {
            this.validationErrors[item.name] =
              "Это поле обязательно для заполнения";
            isValid = false;
          }

          if (item.name === "repeat-pass" && value !== this.formData["pass"]) {
            this.validationErrors[item.name] = "Пароли не совпадают";
            isValid = false;
          }
        }); 
      });

      return isValid;
    },
  },
  computed: {
    componentMap() {
      return {
        input: "FormInput",
        select: "FormSelect",
        radio: "FormRadio",
        password: "FormPassword",
      };
    },
  },
  components: { FormInput, FormSelect, FormRadio, FormPassword },
};
</script>

<style scoped>
.form-builder {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

.form-container {
  display: flex;
  gap: 20px;
}

.form-container > div {
  flex: 1;
}

form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 1.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f5f5f5;
  width: 100%;
  max-width: 600px;
}

.form-footer {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  margin-top: 1rem;
}

button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  background-color: #4caf50;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s;
}

button.danger {
  background-color: #f44336;
}
</style>
