<script setup lang="ts">
import { ref } from 'vue';
import useVuelidate from '@vuelidate/core';
import { required, email, minLength, maxLength, helpers } from '@vuelidate/validators';


const plusOptionalNumeric = helpers.regex(/^\+?\d+$/);

const form = ref({
  gender: "",
  firstName: "",
  lastName: "",
  department: "",
  postalCode: "",
  message: "",
  city: "",
  street: "",
  email: "",
  telephone: "",
  terms: false,
});

const statusMessage = ref("");

const rules = {
  gender: { required: helpers.withMessage('Please select a gender.', required) },
  firstName: {
    required: helpers.withMessage('First name is required.', required),
    noSpaces: helpers.withMessage('First name must not contain spaces.', helpers.regex(/^\S+$/)),
    onlyLetters: helpers.withMessage('First name must only contain letters.', helpers.regex(/^[a-zA-Z]+$/)),
  },
  lastName: {
    required: helpers.withMessage('Last name is required.', required),
    noSpaces: helpers.withMessage('Last name must not contain spaces.', helpers.regex(/^\S+$/)),
    onlyLetters: helpers.withMessage('Last name must only contain letters.', helpers.regex(/^[a-zA-Z]+$/)),
  },
  department: { required: helpers.withMessage("Please select a department.", required) },
  message: { required: helpers.withMessage('Message is required', required) },
  postalCode: {
    required: helpers.withMessage('Postal code is required.', required),
    // Keep your numeric rules for postalCode
    numeric: helpers.withMessage('Postal code must contain only numbers.', helpers.regex(/^\d+$/)),
    minLength: helpers.withMessage('Postal code must be 4 digits.', minLength(4)),
    maxLength: helpers.withMessage('Postal code must be max. 5 digits.', maxLength(5)),
  },
  city: {
    required: helpers.withMessage('City is required.', required),
    onlyLetters: helpers.withMessage('City must only contain letters.', helpers.regex(/^[a-zA-Z\s]+$/)),
  },
  street: { required: helpers.withMessage('Street is required.', required) },
  email: {
    required: helpers.withMessage('Email is required.', required),
    email: helpers.withMessage('Please enter a valid email address.', email),
  },
  telephone: {
    required: helpers.withMessage('Telephone number is required.', required),
    plusOptionalNumeric: helpers.withMessage(
      'Telephone must contain only numbers (with optional leading +).',
      plusOptionalNumeric
    ),
    minLength: helpers.withMessage('Telephone must be at least 10 digits.', minLength(10)),
    maxLength: helpers.withMessage('Telephone must be at most 15 digits.', maxLength(15)),
  },
  terms: { required: helpers.withMessage('You must accept the terms and conditions.', required) },
};

// Initialize vuelidate
const v$ = useVuelidate(rules, form);

const focusFirstInvalidField = () => {
  const firstInvalid = Object.entries(v$.value).find(([_key, field]) => field?.$error);
  if (firstInvalid) {
    const [key] = firstInvalid;
    const invalidElement = document.querySelector(`#${key}`) as HTMLElement; // Cast to HTMLElement
    if (invalidElement && typeof invalidElement.focus === "function") {
      invalidElement.focus();
    }
  }
};


const submitForm = () => {
  v$.value.$touch();

  if (v$.value.$invalid) {
    focusFirstInvalidField();
    statusMessage.value = "Please correct the errors in the highlighted fields.";
    return;
  }

  statusMessage.value = "Form submitted successfully!";
  console.log("Form submitted successfully!", form.value);
};
</script>


<template>
  <main role="main" class="main-content flex justify-center items-center min-h-screen bg-gray-200">
    <div class="flex justify-center items-center min-h-screen bg-gray-200">
      <form @submit.prevent="submitForm" class="form" aria-labelledby="form-title" novalidate>
        <h1 id="form-title" class="text-xl font-bold mb-4">Contact Form</h1>

        <!-- Gender -->
        <div class="gender-group mb-4">
          <label>
            <input type="radio" name="gender" value="man" v-model="form.gender" />
            Man
          </label>
          <label>
            <input type="radio" name="gender" value="woman" v-model="form.gender" />
            Woman
          </label>
          <label>
            <input type="radio" name="gender" value="other" v-model="form.gender" />
            Other
          </label>
        </div>
        <p v-if="v$.gender.$error" class="text-red-500" aria-live="assertive" id="gender-error">
          {{ v$.gender.$errors[0]?.$message }}
        </p>

        <!-- First Name -->
        <div class="mb-4">
          <label for="firstName">First Name</label>
          <input id="firstName" type="text" class="input" v-model="form.firstName" :aria-invalid="v$.firstName.$error"
            aria-describedby="firstName-error" />
          <p v-if="v$.firstName.$error" class="text-red-500" aria-live="assertive" id="firstName-error">
            {{ v$.firstName.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Last Name -->
        <div class="mb-4">
          <label for="lastName">Last Name</label>
          <input id="lastName" type="text" class="input" v-model="form.lastName" :aria-invalid="v$.lastName.$error"
            aria-describedby="lastName-error" />
          <p v-if="v$.lastName.$error" class="text-red-500" aria-live="assertive" id="lastName-error">
            {{ v$.lastName.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Department -->
        <div class="mb-4">
          <label for="department">Select a Department</label>
          <select id="department" class="select" v-model="form.department" :aria-invalid="v$.department?.$error"
            aria-describedby="department-error">
            <option value="" disabled>-- Select Department --</option>
            <option value="health">Health Department</option>
            <option value="education">Education Department</option>
            <option value="transport">Transport Department</option>
            <option value="immigration">Immigration Office</option>
          </select>
          <p v-if="v$.department?.$error" class="text-red-500" aria-live="assertive" id="department-error">
            {{ v$.department.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Message -->
        <div class="mb-4">
          <label for="message">Message</label>
          <input id="message" type="text" class="input" v-model="form.message" :aria-invalid="v$.message.$error"
            aria-describedby="message-error" />
          <p v-if="v$.message.$error" class="text-red-500" aria-live="assertive" id="message-error">
            {{ v$.message.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Postal Code -->
        <div class="mb-4">
          <label for="postalCode">Postal Code</label>
          <input id="postalCode" type="text" class="input" v-model="form.postalCode"
            :aria-invalid="v$.postalCode.$error" aria-describedby="postalCode-error" />
          <p v-if="v$.postalCode.$error" class="text-red-500" aria-live="assertive" id="postalCode-error">
            {{ v$.postalCode.$errors[0]?.$message }}
          </p>
        </div>

        <!-- City -->
        <div class="mb-4">
          <label for="city">City</label>
          <input id="city" type="text" class="input" v-model="form.city" :aria-invalid="v$.city.$error"
            aria-describedby="city-error" />
          <p v-if="v$.city.$error" class="text-red-500" aria-live="assertive" id="city-error">
            {{ v$.city.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Street -->
        <div class="mb-4">
          <label for="street">Street</label>
          <input id="street" type="text" class="input" v-model="form.street" :aria-invalid="v$.street.$error"
            aria-describedby="street-error" />
          <p v-if="v$.street.$error" class="text-red-500" aria-live="assertive" id="street-error">
            {{ v$.street.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Email -->
        <div class="mb-4">
          <label for="email">Email</label>
          <input id="email" type="email" class="input" v-model="form.email" :aria-invalid="v$.email.$error"
            aria-describedby="email-error" />
          <p v-if="v$.email.$error" class="text-red-500" aria-live="assertive" id="email-error">
            {{ v$.email.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Telephone -->
        <div class="mb-4">
          <label for="telephone">Telephone</label>
          <input id="telephone" type="tel" class="input" v-model="form.telephone" :aria-invalid="v$.telephone.$error"
            aria-describedby="telephone-error" />
          <p v-if="v$.telephone.$error" class="text-red-500" aria-live="assertive" id="telephone-error">
            {{ v$.telephone.$errors[0]?.$message }}
          </p>
        </div>

        <!-- Terms -->
        <div class="mb-4">
          <label>
            <input type="checkbox" v-model="form.terms" :aria-invalid="v$.terms.$error" aria-describedby="terms-error" />
            I agree to the terms and conditions
          </label>
        </div>
        <p v-if="v$.terms.$error" class="text-red-500" aria-live="assertive" id="terms-error">
          {{ v$.terms.$errors[0]?.$message }}
        </p>

        <!-- Submit -->
        <button type="submit" class="btn-primary">Submit</button>

        <!-- Status Message -->
        <div aria-live="assertive" class="mt-4">{{ statusMessage }}</div>
      </form>
    </div>
  </main>
</template>

<style>
.btn-primary {
  background-color: #0056b3;
  color: #ffffff;
}
</style>
