<template>
  <div class="form-builder">
    <form @submit.prevent="onSubmit" @reset.prevent="onReset">
      <component
        v-for="item in config"
        :key="item.id"
        :is="componentMap[item.type]"
        :label="item.label"
        :name="item.name"
        :options="item.additional?.options"
        v-model="formData[item.name]"
        :value="formData[item.name]"
        @input="updateFormData(item.name, $event.target.value)"
        :error="validationErrors[item.name]"
      />

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
      type: Array,
      required: true
    }
  },
  data() {
    return {
      formData: {},
      validationErrors: {}
    };
  },
  created() {
    this.initFormData();
  },
  methods: {
    initFormData() {
      this.config.forEach(item => {
        this.formData[item.name] = item.additional?.options
          ? item.additional.options.find(option => option.selected)?.value || ""
          : "";
      });
    },
    onSubmit(e) {
      if (this.validateForm()) {
        const formData = new FormData(e.target);
        fetch("https://api.example.com/endpoint", {
          method: "POST",
          body: formData
        })
          .then(response => {
            if (response.ok) {
              this.onReset();
            }
          })
          .catch(error => {});
      }
    },
    onReset() {
      this.config
        .filter(item => item.type !== "select" && item.type !== "radio")
        .forEach(item => {
          this.formData[item.name] = "";
        });
    },
    validateForm() {
      this.validationErrors = {};
      let isValid = true;

      this.config.forEach(item => {
        const value = this.formData[item.name];
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

      return isValid;
    },
    updateFormData(name, value) {
      this.formData[name] = value;
    }
  },
  computed: {
    componentMap() {
      return {
        input: "FormInput",
        select: "FormSelect",
        radio: "FormRadio",
        password: "FormPassword"
      };
    }
  },
  components: { FormInput, FormSelect, FormRadio, FormPassword }
};
</script>

<style scoped>
.form-builder {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
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
  max-width: 400px;
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
