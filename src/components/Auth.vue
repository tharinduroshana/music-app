<template>
  <div class="fixed z-10 inset-0 overflow-y-auto" id="modal" :class="hiddenClass">
    <div
      class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
    >
      <div class="fixed inset-0 transition-opacity">
        <div class="absolute inset-0 bg-gray-800 opacity-75"></div>
      </div>

      <!-- This element is to trick the browser into centering the modal contents. -->
      <span class="hidden sm:inline-block sm:align-middle sm:h-screen">&#8203;</span>

      <div
        class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
      >
        <!-- Add margin if you want to see some of the overlay behind the modal-->
        <div class="py-4 text-left px-6">
          <!--Title-->
          <div class="flex justify-between items-center pb-4">
            <p class="text-2xl font-bold">Your Account</p>
            <!-- Modal Close Button -->
            <div class="modal-close cursor-pointer z-50" @click="modalVisibility = false">
              <i class="fas fa-times"></i>
            </div>
          </div>

          <!-- Tabs -->
          <ul class="flex flex-wrap mb-4">
            <li class="flex-auto text-center">
              <a
                class="block rounded py-3 px-4 transition"
                href="#"
                @click.prevent="tab = 'login'"
                :class="{
                  'hover:text-white text-white bg-blue-600': tab === 'login',
                  'hover:text-blue-600': tab === 'register'
                }"
                >Login</a
              >
            </li>
            <li class="flex-auto text-center">
              <a
                class="block rounded py-3 px-4 transition"
                href="#"
                :class="{
                  'hover:text-white text-white bg-blue-600': tab === 'register',
                  'hover:text-blue-600': tab === 'login'
                }"
                @click.prevent="tab = 'register'"
                >Register</a
              >
            </li>
          </ul>

          <!-- Login Form -->
          <vee-form v-show="tab === 'login'" :validation-schema="loginSchema" @submit="login">
            <!-- Email -->
            <div class="mb-3">
              <label class="inline-block mb-2">Email</label>
              <vee-field
                name="email"
                type="email"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
                placeholder="Enter Email"
              />
              <ErrorMessage class="text-red-600" name="email" />
            </div>
            <!-- Password -->
            <div class="mb-3">
              <label class="inline-block mb-2">Password</label>
              <vee-field
                name="password"
                type="password"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
                placeholder="Password"
              />
              <ErrorMessage class="text-red-600" name="password" />
            </div>
            <button
              type="submit"
              class="block w-full bg-purple-600 text-white py-1.5 px-3 rounded transition hover:bg-purple-700"
            >
              Submit
            </button>
          </vee-form>
          <!-- Registration Form -->
          <div
            class="text-white text-center font-bold p-4 rounded mb-4"
            v-if="reg_show_alert"
            :class="reg_alert_variant"
          >
            {{ reg_alert_msg }}
          </div>
          <vee-form
            v-show="tab === 'register'"
            :validation-schema="schema"
            @submit="register"
            :initial-values="userData"
          >
            <!-- Name -->
            <div class="mb-3">
              <label class="inline-block mb-2">Name</label>
              <vee-field
                type="text"
                name="name"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
                placeholder="Enter Name"
              />
              <ErrorMessage class="text-red-600" name="name" />
            </div>
            <!-- Email -->
            <div class="mb-3">
              <label class="inline-block mb-2">Email</label>
              <vee-field
                name="email"
                type="email"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
                placeholder="Enter Email"
              />
              <ErrorMessage class="text-red-600" name="email" />
            </div>
            <!-- Age -->
            <div class="mb-3">
              <label class="inline-block mb-2">Age</label>
              <vee-field
                type="number"
                name="age"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
              />
              <ErrorMessage class="text-red-600" name="age" />
            </div>
            <!-- Password -->
            <div class="mb-3">
              <label class="inline-block mb-2">Password</label>
              <vee-field name="password" :bails="false" v-slot="{ field, errors }">
                <input
                  class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
                  placeholder="Password"
                  type="password"
                  v-bind="field"
                />
                <div :key="error" class="text-red-600" v-for="error in errors">
                  {{ error }}
                </div>
              </vee-field>
              <ErrorMessage class="text-red-600" name="password" />
            </div>
            <!-- Confirm Password -->
            <div class="mb-3">
              <label class="inline-block mb-2">Confirm Password</label>
              <vee-field
                name="confirm_password"
                type="password"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
                placeholder="Confirm Password"
              />
              <ErrorMessage class="text-red-600" name="confirm_password" />
            </div>
            <!-- Country -->
            <div class="mb-3">
              <label class="inline-block mb-2">Country</label>
              <vee-field
                as="select"
                name="country"
                class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
              >
                <option value="USA">USA</option>
                <option value="Mexico">Mexico</option>
                <option value="Germany">Germany</option>
                <option value="Antarctica">Antarctica</option>
              </vee-field>
              <ErrorMessage class="text-red-600" name="country" />
            </div>
            <!-- TOS -->
            <div class="mb-3 pl-6">
              <vee-field
                type="checkbox"
                name="tos"
                value="1"
                class="w-4 h-4 float-left -ml-6 mt-1 rounded"
              />
              <label class="inline-block">Accept terms of service</label>
              <ErrorMessage class="text-red-600 block" name="tos" />
            </div>
            <button
              type="submit"
              class="block w-full bg-purple-600 text-white py-1.5 px-3 rounded transition hover:bg-purple-700"
              :disabled="reg_in_submission"
            >
              Submit
            </button>
          </vee-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import useModalStore from '@/stores/modal'
import { mapState, mapWritableState } from 'pinia'

export default {
  name: 'Auth',
  data() {
    return {
      tab: 'login',
      schema: {
        name: 'required|min:3|max:100|alpha_spaces',
        email: 'required|email|min:3|max:100',
        age: 'required|min_value:18|max_value:100',
        password: 'required|min:3|max:100|exclude:password',
        confirm_password: 'required|password_mismatch:@password',
        country: 'required|country_exclude:Antarctica',
        tos: 'tos'
      },
      userData: {
        country: 'USA'
      },
      reg_in_submission: false,
      reg_show_alert: false,
      reg_alert_variant: 'bg-blue-500',
      reg_alert_msg: 'Please wait! Registering...',
      loginSchema: {
        email: 'required|email',
        password: 'required|min:3|max:100'
      }
    }
  },
  computed: {
    ...mapState(useModalStore, ['hiddenClass']),
    ...mapWritableState(useModalStore, {
      modalVisibility: 'isOpen'
    })
  },
  methods: {
    register(values) {
      this.reg_in_submission = true
      this.reg_show_alert = true
      this.reg_alert_variant = 'bg-blue-500'
      this.reg_alert_msg = 'Please wait! Registering...'

      this.reg_alert_variant = 'bg-green-500'
      this.reg_alert_msg = 'Registration successful!'
      console.log('Registering...', values)
    },
    login(values) {
      console.log('Logging in...', values)
    }
  }
}
</script>
