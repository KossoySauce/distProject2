<script setup>
import { ref } from 'vue'
import emailValidator from 'email-validator'
import { useRouter } from 'vue-router'
import Header from '../components/Header.vue'

import { useUserStore } from '../../src/stores/user.js'

const store = useUserStore()

const router = useRouter()

const errorText = ref('-')

const username = ref('')
const email = ref('')
const password = ref('')
const confirmPass = ref('')

const usernameValid = ref(false)
const emailValid = ref(false)
const passwordValid = ref(false)
const confirmPassValid = ref(false)
const avatarValid = ref(false)

localStorage.clear()

// function to set the avator in local storage for the user
function setUserAvatar(numberAvatar) {
  let image = ''
  switch (numberAvatar) {
    case 1:
      image = '../../public/avatar-option1.png'
      break
    case 2:
      image = '../../public/avatar-option2.png'
      break
    case 3:
      image = '../../public/avatar-option3.png'
      break
    case 4:
      image = '../../public/avatar-option4.png'
      break
    case 5:
      image = '../../public/avatar-option5.png'
      break
    case 6:
      image = '../../public/avatar-option6.png'
      break
    default:
      return
  }

  localStorage.setItem('userAvatar', image)
}

// triggershake to whatever element i need and a timer to set it back
const triggerShake = (field) => {
  field.value = true
  setTimeout(() => {
    field.value = false
  }, 300)
}

// this will trigger a error text and then reset it

const triggerError = (error) => {
  errorText.value = error
  setTimeout(() => {
    errorText.value = '-'
  }, 1000)
}

// when everything is check and good then this will create the user
const createUser = async (username, email, password) => {
  const data = {
    username,
    email,
    password,
  }

  try {
    // register a user
    const url = 'https://csci-430-server-dubbabadgmf8hpfk.eastus2-01.azurewebsites.net/user'

    const options = {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    }

    const response = await fetch(url, options)

    if (response.status === 201) {
      const data = await response.json()

      console.log(data)

      store.setUserData(data)

      router.push({
        path: '/home',
      })
    } else {
      console.log('somethings wrong: ', response.status)
    }
  } catch (err) {
    console.log('fetch error: ' + err)
  }
}

const check = async (event) => {
  event.preventDefault()

  let error = false

  // checking

  if (!username.value.trim()) {
    triggerShake(usernameValid)
    error = true
  }

  if (!email.value.trim()) {
    triggerShake(emailValid)
    error = true
  }

  if (!password.value.trim()) {
    triggerShake(passwordValid)
    error = true
  }

  if (!confirmPass.value.trim()) {
    triggerShake(confirmPassValid)
    error = true
  }

  if (error) {
    triggerError('* Empty Fields *')
    return
  }

  // check if user picked a avatar

  if (!localStorage.getItem('userAvatar')) {
    triggerShake(avatarValid)
    triggerError('* Forgot to pick Avatar *')
    return
  }

  // checking if the email is valid

  if (!emailValidator.validate(email.value)) {
    triggerShake(emailValid)
    triggerError('* Invalid Email *')
    return
  }

  // check password length

  if (password.value.length < 8) {
    triggerShake(passwordValid)
    triggerShake(confirmPassValid)
    triggerError('* password needs to be 8 or more characters')
    return
  }

  // checking if the passwords match
  if (password.value !== confirmPass.value) {
    triggerShake(passwordValid)
    triggerShake(confirmPassValid)
    triggerError('* Passwords do not match *')
    return
  }

  createUser(username.value, email.value, password.value)
}
</script>

<template>





  <Header>
    <template #user>
      <RouterLink to="/">Welcome</RouterLink>
      <RouterLink to="/signin" class="signin-button">Log In</RouterLink>

    </template>
  </Header>


  <div class="join-container">
    <div class="join-prompt">
      <div class="title-container">
        <h2>Welcome New User! Enter Information</h2>
      </div>

      <form class="join-inputs-container">
        <div class="group-input">
          <div class="label-input">Username</div>
          <input type="text" v-model="username" :class="{ shake: usernameValid }" />
        </div>
        <div class="group-input">
          <div class="label-input">Email</div>
          <input type="email" v-model="email" :class="{ shake: emailValid }" />
        </div>
        <div class="group-input">
          <div class="label-input">Password</div>
          <input type="password" v-model="password" :class="{ shake: passwordValid }" />
        </div>
        <div class="group-input">
          <div class="label-input">Confirm Password</div>
          <input
            type="password"
            id="confirmPass"
            v-model="confirmPass"
            :class="{ shake: confirmPassValid }"
          />
        </div>
        <div class="avatar-selection-container" :class="{ shake: avatarValid }">
          <div class="avatar-container" tabindex="0" @click="setUserAvatar(1)">
            <img src="../../public/avatar-option1.png" alt="" class="avatar-option-join" />
          </div>
          <div class="avatar-container" tabindex="0" @click="setUserAvatar(2)">
            <img src="../../public/avatar-option2.png" alt="" class="avatar-option-join" />
          </div>
          <div class="avatar-container" tabindex="0" @click="setUserAvatar(3)">
            <img src="../../public/avatar-option3.png" alt="" class="avatar-option-join" />
          </div>
          <div class="avatar-container" tabindex="0" @click="setUserAvatar(4)">
            <img src="../../public/avatar-option4.png" alt="" class="avatar-option-join" />
          </div>
          <div class="avatar-container" tabindex="0" @click="setUserAvatar(5)">
            <img src="../../public/avatar-option5.png" alt="" class="avatar-option-join" />
          </div>
          <div class="avatar-container" tabindex="0" @click="setUserAvatar(6)">
            <img src="../../public/avatar-option6.png" alt="" class="avatar-option-join" />
          </div>
        </div>
      </form>
      <div class="error-container">
        <p class="error-text">{{ errorText }}</p>
      </div>

      <div class="button-container">
        <button type="submit" @click.prevent="check">Proceed</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* sign in button */

.signin-button {
  color: rgb(138, 39, 39);
}

/* container holding all the avatar options */
.avatar-selection-container {
  display: flex;
  margin-top: 2rem;
  gap: 2rem;
}

/* on hover of image i want it to scale */
.avatar-container:hover {
  scale: 1.5;
  cursor: pointer;
}

/* when avatar is clicked */
.avatar-container:focus {
  scale: 1.5;
}
/* avatar sizes in join page */
.avatar-option-join {
  width: 40px;
}

.links-navbar {
  display: flex;
  margin-inline: 3rem;
  gap: 2rem;
}

/* holds the join prompt with everything. just to make sure everything is layed out in the middle */

.join-container {
  margin-top: 3rem;

  height: 600px;
  display: flex;
  justify-content: center;
}

/* error container */
.error-container {
  width: 500px;
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}

/* label inputs for the input fields */
.label-input {
  font-family: var(--font-primary);
  font-style: italic;
}

/* title container is just holding the message in the join prompt */
.title-container {
  margin-block: 1rem;
}

/* holding all the the inputs labels and titles */
.join-prompt {
  box-shadow: 5px 5px 20px 1px;
  border-radius: 5px;

  width: 500px;
  height: 600px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* group input is the label and input together */
.group-input {
  display: flex;
  flex-direction: column;
  width: 400px;
  margin-top: 1rem;
}

/* styling the input  */
.group-input input {
  height: 40px;
  border: none;
  border-bottom: 1px solid black;
  outline: none;
  background-color: rgba(237, 235, 235, 0.607);
  padding-left: 0.5rem;
  font-family: var(--font-primary);
}

/* styling the input on focus */
.group-input input:focus {
  background-color: rgba(201, 225, 227, 0.406);
}

/* button container join prompt */
.button-container {
  margin-bottom: 1rem;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* button styling */
.button-container button {
  width: 150px;
  height: 40px;
  font-family: var(--font-primary);
  border-radius: 5px;
  border: none;
  transition: background-color 1s ease;
}

/* button hover */
.button-container button:hover {
  background-color: rgb(173, 170, 170);
  cursor: pointer;
}

/* error */
.error-text {
  margin: 0;
  color: rgb(188, 70, 70);
  font-family: var(--font-primary);
  font-size: 20px;
}

/* h2 title */
h2 {
  font-family: var(--font-primary);
  font-size: 23px;
}
</style>
