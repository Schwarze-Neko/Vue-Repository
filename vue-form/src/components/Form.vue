<script setup lang="ts">
import { ref } from 'vue';
import useVuelidate from '@vuelidate/core';
import { required, email, numeric, minLength, maxLength, helpers } from '@vuelidate/validators';


const form = ref({
  gender: '',
  firstName: '',
  lastName: '',
  postalCode: '',
  city: '',
  street: '',
  email: '',
  telephone: '',
  terms: false,
});


const rules = {
  gender: {
    required: helpers.withMessage('Please select a gender.', required),
  },
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
  postalCode: {
    required: helpers.withMessage('Postal code is required.', required),
    numeric: helpers.withMessage('Postal code must contain only numbers.', numeric),
    minLength: helpers.withMessage('Postal code must be 5 digits.', minLength(5)),
    maxLength: helpers.withMessage('Postal code must be 5 digits.', maxLength(5)),
  },
  city: {
    onlyLetters: helpers.withMessage('City cannot contain numbers or special characters', helpers.regex(/^[a-zA-Z\s]+$/)),
    required: helpers.withMessage('City is required.', required),
  },
  street: {
    required: helpers.withMessage('Street is required.', required),
  },
  email: {
    required: helpers.withMessage('Email is required.', required),
    email: helpers.withMessage('Please enter a valid email address.', email),
  },
  telephone: {
    required: helpers.withMessage('Telephone number is required.', required),
    numeric: helpers.withMessage('Telephone number must contain only numbers.', numeric),
  },
  terms: {
    required: helpers.withMessage('You must accept the terms and conditions.', required),
  },
};

// Initialize Vuelidate
const v$ = useVuelidate(rules, form);

// Form submission
const submitForm = () => {
  v$.value.$touch();
  if (v$.value.$invalid) {
    console.log('Form contains errors');
  } else {
    console.log('Form submitted successfully!', form.value);
  }
};
</script>

<template>
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
      </div>
      <p v-if="v$.gender.$error" class="text-red-500">{{ v$.gender.$errors[0].$message }}</p>

      <!-- First Name -->
      <div class="mb-4">
        <label for="firstName">First Name</label>
        <input id="firstName" type="text" class="input" v-model="form.firstName" :aria-invalid="v$.firstName.$error" />
        <p v-if="v$.firstName.$error" class="text-red-500">{{ v$.firstName.$errors[0].$message }}</p>
      </div>

      <!-- Last Name -->
      <div class="mb-4">
        <label for="lastName">Last Name</label>
        <input id="lastName" type="text" class="input" v-model="form.lastName" :aria-invalid="v$.lastName.$error" />
        <p v-if="v$.lastName.$error" class="text-red-500">{{ v$.lastName.$errors[0].$message }}</p>
      </div>

      <!-- Postal Code -->
      <div class="mb-4">
        <label for="postalCode">Postal Code</label>
        <input id="postalCode" type="text" class="input" v-model="form.postalCode"
          :aria-invalid="v$.postalCode.$error" />
        <p v-if="v$.postalCode.$error" class="text-red-500">{{ v$.postalCode.$errors[0].$message }}</p>
      </div>

      <!-- City -->
      <div class="mb-4">
        <label for="city">City</label>
        <input id="city" type="text" class="input" v-model="form.city" :aria-invalid="v$.city.$error" />
        <p v-if="v$.city.$error" class="text-red-500">{{ v$.city.$errors[0].$message }}</p>
      </div>

      <!-- Street -->
      <div class="mb-4">
        <label for="street">Street</label>
        <input id="street" type="text" class="input" v-model="form.street" :aria-invalid="v$.street.$error" />
        <p v-if="v$.street.$error" class="text-red-500">{{ v$.street.$errors[0].$message }}</p>
      </div>

      <!-- Email -->
      <div class="mb-4">
        <label for="email">Email</label>
        <input id="email" type="email" class="input" v-model="form.email" :aria-invalid="v$.email.$error" />
        <p v-if="v$.email.$error" class="text-red-500">{{ v$.email.$errors[0].$message }}</p>
      </div>

      <!-- Telephone -->
      <div class="mb-4">
        <label for="telephone">Telephone</label>
        <input id="telephone" type="tel" class="input" v-model="form.telephone" :aria-invalid="v$.telephone.$error" />
        <p v-if="v$.telephone.$error" class="text-red-500">{{ v$.telephone.$errors[0].$message }}</p>
      </div>

      <!-- Terms -->
      <div class="mb-4">
        <label>
          <input type="checkbox" v-model="form.terms" />
          I agree to the terms and conditions
        </label>
      </div>
      <p v-if="v$.terms.$error" class="text-red-500">{{ v$.terms.$errors[0].$message }}</p>

      <!-- Submit -->
      <button type="submit" class="btn btn-primary">Submit</button>
    </form>
  </div>
</template>
