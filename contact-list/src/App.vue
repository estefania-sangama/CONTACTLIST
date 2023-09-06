<template>
  <div>
    <div>
      <h1 class="title">Add New Contact</h1>
      <form>
        <label>Name (ie. Jhon):</label>
        <input type="name" required v-model.trim="name" />
        <div v-if="nameError" class="warning">{{ nameError }}</div>

        <label>Email (ie. roo@bar.com):</label>
        <input type="email" required v-model.trim="email" />
        <div v-if="emailError" class="warning">{{ emailError }}</div>

        <label>Phone Number (ie. 555-555-5555):</label>
        <input type="phone" required v-model.trim="phone" />
        <div v-if="phoneError" class="warning">{{ phoneError }}</div>
      </form>
    </div>

    <div class="submit">
      <button class="btn btn-primary" @click="saveContact">Add Contact</button>
      <div v-if="isLoading" class="loading">Loading...</div>
      <div v-if="errorMessage" class="warning">{{ errorMessage }}</div>
      <div v-if="successMessage" class="success">{{ successMessage }}</div>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  data() {
    return {
      name: '',
      email: '',
      phone: '',
      postId: '',
      nameError: '',
      emailError: '',
      phoneError: '',
      errorMessage: '',
      successMessage: '',
      isLoading: false
    }
  },

  methods: {
    saveContact() {
      this.validateSubmit()
      if (this.nameError != '' || this.emailError != '' || this.phoneError != '') {
        return
      }
      const requestOptions = {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: this.name, email: this.email, phone: this.phone })
      }

      this.isLoading = true

      fetch(
        'https://8j5baasof2.execute-api.us-west-2.amazonaws.com/production/tests/trucode/items',
        requestOptions
      ).then(async (response) => {
        const data = await response.json()

        this.isLoading = false

        //check for error response
        if (!response.ok) {
          this.errorMessage = data.error
        } else {
          this.name = ''
          this.email = ''
          this.phone = ''
          this.successMessage = 'You added a new contact successfully'
        }
      })
    },
    validateSubmit() {
      //Validate name
      this.nameError = this.name.length > 0 ? '' : 'This field is mandatory'

      const emailRegex =
        /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|.(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
      //Validate email
      this.emailError = emailRegex.test(this.email.toLowerCase())
        ? ''
        : 'Please enter a valid email'

      const phoneRegex = /\d{3}-\d{3}-\d{4}/
      this.phoneError = phoneRegex.test(this.phone)
        ? ''
        : 'Please enter phone number in the form ###-###-####'
    }
  }
}
</script>

<style>
.title {
  color: #315171;
  font-size: 1.5em;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: bold;
  text-align: center;
}

form {
  max-width: 420px;
  margin: 30px auto;
  background: white;
  text-align: left;
  padding: 40px;
  border-radius: 10px;
}

label {
  color: #aaa;
  display: inline-block;
  margin: 25px 0 15px;
  font-size: 0.7em;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: bold;
}

input {
  display: block;
  padding: 10px 6px;
  width: 100%;
  box-sizing: border-box;
  border: none;
  border-bottom: 1px solid #ddd;
  color: #555;
}

.warning {
  color: red;
  font-size: 0.7em;
}

.success {
  color: green;
  font-size: 0.7em;
}

.loading {
  font-size: 0.7em;
}

.submit {
  font-weight: bold;
  text-align: center;
}
</style>
