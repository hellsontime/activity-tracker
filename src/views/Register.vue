<template>
  <div class="max-w-screen-sm mx-auto px-4 py-10">
    <!-- Error handling -->
    <div v-if="errorMsg" class="mb-10 p-4 rounded-md bg-light-grey shadow-lg">
      <p class="text-red-500">{{ errorMsg }}</p>
    </div>

    <!-- Registration form -->
    <form @submit.prevent="register" class="p-8 flex flex-col rounded-md shadow-lg bg-light-grey">
      <h1 class="text-3xl text-at-light-greend mb-4">Register</h1>

      <div class="flex flex-col mb-2 ">
        <label for="email" class="mb-1 text-sm text-at-light-green">Email</label>
        <input type="email" required class="p-2 text-gray-500 focus:outline-none" id="email" v-model="email">
      </div>

      <div class="flex flex-col mb-2 ">
        <label for="password" class="mb-1 text-sm text-at-light-green">Password</label>
        <input type="password" required class="p-2 text-gray-500 focus:outline-none" id="password" v-model="password">
      </div>

      <div class="flex flex-col mb-2 ">
        <label for="passwordConfirm" class="mb-1 text-sm text-at-light-green">Confirm Password</label>
        <input type="password" required class="p-2 text-gray-500 focus:outline-none" id="passwordConfirm" v-model="passwordConfirm">
      </div>

      <button type="submit" class="mt-6 py-2 px-6 rounded-sm self-start text-sm text-white bg-at-light-green duration-200 border-solid border-2 border-transparent hover:border-at-light-green hover:bg-white hover:text-at-light-green">Register</button>

      <router-link class="text-sm mt-6 text-center" :to="{name: 'Login'}">
        Already have an account? <span class="text-at-light-green">Login</span>
      </router-link>
    </form>
  </div>

  
</template>

<script>
import { ref } from 'vue'
import { supabase } from '../supabase/init';
import { useRouter } from 'vue-router';

export default {
  name: "register",
  setup() {
    // Create data / vars
    const router = useRouter()
    const email = ref(null)
    const password = ref(null)
    const passwordConfirm = ref(null)
    const errorMsg = ref(null)

    // Register function

    const register = async () => {
      if (password.value === passwordConfirm.value) {
        try {
          let { error } = await supabase.auth.signUp({
            email: email.value,
            password: password.value
          })
          if (error) throw error
          router.push({ name: 'Login' })
        }
        catch(error) {
          errorMsg.value = `Error: ${error.message}`
          setTimeout(() => {
            errorMsg.value = false
          }, 5000)
        }  
        return
      }

      errorMsg.value = 'Error: Password do not match'
      setTimeout(() => {
        errorMsg.value = false
      }, 5000)
    }

    return { email, password, passwordConfirm, errorMsg, register};
  },
};
</script>
