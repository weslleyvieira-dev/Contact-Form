<script setup>
import { ref, reactive, watch, computed } from "vue";

const isSubmitted = ref(false);
const notification = ref("");

const formData = reactive({
  firstName: "",
  lastName: "",
  email: "",
  queryType: "",
  message: "",
  checkbox: false,
});

const errors = reactive({
  firstName: "",
  lastName: "",
  email: "",
  queryType: "",
  message: "",
  checkbox: "",
});

const isValid = computed(() => {
  return (
    Object.values(errors).every((error) => error === "") &&
    Object.values(formData).every((value) => value !== false && value !== "")
  );
});

function validateForm() {
  Object.keys(errors).forEach((key) => (errors[key] = ""));

  if (!formData.firstName) {
    errors.firstName = "This field is required";
  }

  if (!formData.lastName) {
    errors.lastName = "This field is required";
  }

  if (!/\S+@\S+\.\S+/.test(formData.email)) {
    errors.email = "Please enter a valid email address";
  }

  if (!formData.queryType) {
    errors.queryType = "Please select a query type";
  }

  if (!formData.message) {
    errors.message = "This field is required";
  }

  if (!formData.checkbox) {
    errors.checkbox = "To submit this form, please consent to being contacted";
  }

  return Object.values(errors).every((error) => error === "");
}

function submitForm() {
  isSubmitted.value = true;
  if (validateForm()) {
    clearForm();
    notification.value = "Message Sent!";
    setTimeout(() => {
      notification.value = "";
    }, 5000);
  } else {
    notification.value = "Please fix the errors before submitting.";
    setTimeout(() => {
      notification.value = "";
    }, 5000);
  }
}

function clearForm() {
  Object.keys(formData).forEach((key) => (formData[key] = ""));
}

watch(
  () => formData,
  (newValue) => {
    Object.keys(errors).forEach((key) => {
      if (newValue[key]) {
        errors[key] = "";
      }
    });
  },
  { deep: true }
);
</script>

<template>
  <main>
    <div v-if="notification" class="notification">
      <div v-if="notification === 'Message Sent!'">
        <h2>
          <img src="./assets/images/icon-success-check.svg" />
          {{ notification }}
        </h2>
        <p>Thanks for completing the form. We'll be in touch soon!</p>
      </div>
      <div v-else>{{ notification }}</div>
    </div>

    <form @submit.prevent="submitForm" class="contact-form">
      <h1>Contact Us</h1>

      <div class="name-group">
        <label class="name-container"
          >First Name *
          <input id="firstName" v-model="formData.firstName" type="text" />
          <span v-if="isSubmitted && errors.firstName" class="error">{{
            errors.firstName
          }}</span>
        </label>

        <label class="name-container"
          >Last Name *
          <input id="lastName" v-model="formData.lastName" type="text" />
          <span v-if="isSubmitted && errors.lastName" class="error">{{
            errors.lastName
          }}</span>
        </label>
      </div>

      <label for="email"
        >Email Address *
        <input id="email" v-model="formData.email" type="email" />
        <span v-if="isSubmitted && errors.email" class="error">{{
          errors.email
        }}</span>
      </label>

      <label for="queryType"
        >Query Type *
        <div class="radio-group">
          <label
            class="radio-container"
            :class="{ selected: formData.queryType === 'General Enquiry' }"
          >
            <input
              class="radio"
              id="generalEnquiry"
              type="radio"
              name="queryType"
              value="General Enquiry"
              v-model="formData.queryType"
            />
            General Enquiry
          </label>
          <label
            class="radio-container"
            :class="{ selected: formData.queryType === 'Support Request' }"
          >
            <input
              class="radio"
              id="supportRequest"
              type="radio"
              name="queryType"
              value="Support Request"
              v-model="formData.queryType"
            />
            Support Request
          </label>
        </div>
      </label>
      <span v-if="isSubmitted && errors.queryType" class="error">{{
        errors.queryType
      }}</span>

      <label for="message"
        >Message *
        <textarea id="message" v-model="formData.message"></textarea>
      </label>
      <span v-if="isSubmitted && errors.message" class="error">{{
        errors.message
      }}</span>

      <label class="checkbox">
        <input type="checkbox" v-model="formData.checkbox" />
        I consent to being contacted by the team *</label
      >
      <span v-if="isSubmitted && errors.checkbox" class="error">{{
        errors.checkbox
      }}</span>

      <input class="submit" type="submit" value="Submit" />
    </form>
  </main>
</template>

<style>
html,
body {
  background-color: hsl(148, 38%, 91%);
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
}

@font-face {
  font-family: "Karla";
  src: url("./assets/fonts/static/Karla-Regular.ttf") format("truetype"),
    url("./assets/fonts/static/Karla-Bold.ttf") format("truetype");
  font-style: normal;
}

* {
  font-family: "Karla", sans-serif;
}
</style>

<style scoped>
* {
  font-weight: 400;
  font-size: 16px;
}

main {
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
}

form {
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin: 0 auto;
  width: 30%;
  background-color: hsl(0, 0%, 100%);
  border-radius: 15px;
  padding: 1.5% 2% 2%;
}

form h1 {
  font-weight: 700;
  font-size: 28px;
  color: hsl(187, 24%, 22%);
}

form div {
  width: 100%;
}

form label {
  display: block;
  width: 100%;
  padding: 0;
}

form input {
  width: 100%;
  height: 5vh;
  border: 1px solid hsl(169, 82%, 27%);
  border-radius: 10px;
  margin: 10px 0;
  box-sizing: border-box;
  padding: 0 20px;
}

form input:hover {
  cursor: pointer;
  border: 1px solid hsl(169, 82%, 22%);
}

form .name-group,
form .radio-group {
  display: flex;
  gap: 20px;
  margin-top: 5px;
}

.radio-container {
  display: flex;
  align-items: center;
  border: 1px solid hsl(169, 82%, 27%);
  border-radius: 7px;
  padding: 7px 20px;
  width: 100%;
  margin: 10px 0;
  transition: background-color 0.5s;
  resize: none;
  box-sizing: border-box;
  height: 5vh;
}

.radio-container input.radio {
  width: 18px;
  margin: 0 15px 0 0;
}

.radio-container,
.checkbox input {
  cursor: pointer;
}

.radio-container:active {
  background-color: hsl(148, 38%, 91%);
}

.radio-container.selected {
  background-color: hsl(148, 38%, 91%);
}

textarea {
  width: 100%;
  height: 10vh;
  border: 1px solid hsl(169, 82%, 27%);
  border-radius: 10px;
  margin: 10px 0 20px;
  resize: none;
  box-sizing: border-box;
  padding: 12px 15px;
  line-height: 1.5;
}

.checkbox {
  display: flex;
  align-items: center;
  justify-content: start;
  gap: 15px;
  margin: 20px 0;
  cursor: pointer;
}

.checkbox input {
  width: 20px;
  height: 20px;
  margin: 0;
}

.submit {
  font-weight: 700;
  color: hsl(0, 0%, 100%);
  background-color: hsl(169, 82%, 27%);
  margin: 10px 0 10px;
  height: 6vh;
}

.submit:hover {
  cursor: pointer;
  background-color: hsl(169, 82%, 12%);
}

.error {
  color: hsl(0, 66%, 54%);
  font-size: 0.9rem;
  margin: 0 0 20px;
  display: block;
}

.error:empty {
  display: none;
}

.submit:disabled {
  background-color: hsl(0, 0%, 80%);
  cursor: not-allowed;
}

.notification {
  position: absolute;
  z-index: 2;
  top: 3vh;
  background-color: hsl(187, 24%, 22%);
  color: hsl(148, 38%, 91%);
  padding: 10px 20px;
  border-radius: 17px;
  font-weight: bold;
  line-height: 1.5;
}

.notification h2 {
  display: flex;
  align-items: center;
  font-weight: 700;
  padding: 0 10px;
}

.notification img {
  padding-right: 10px;
}

.notification p {
  padding: 0 10px;
}

@media (max-width: 412px) and (max-height: 915px) {
  main {
    height: auto;
    padding: 35px 0;
  }

  form {
    width: 80%;
    padding: 10px 23px;
    gap: 10px;
  }

  form .name-group,
  form .radio-group {
    display: block;
    margin: 5px 0;
    width: 100%;
  }

  .name-container {
    padding: 0 0 7px;
  }

  input {
    margin: 7px 0;
  }

  .radio-container {
    margin: 15px 0;
  }

  .radio-container input.radio {
    width: 18px;
    margin: 0 15px 0 0;
  }

  textarea {
    height: 20vh;
    margin: 10px 0 0;
  }

  .checkbox {
    margin: 10px 0;
  }

  .error {
    margin: 0 0 10px;
  }

  .notification {
    position: fixed;
    width: 80%;
    padding: 2px 7px;
  }
}
</style>
