<script setup>
import { ref, computed } from "vue";
import Axios from "axios"; // Import Axios
import { loginUrl } from "@/api/APIUrls";
import router from "@/router";
import {setThemeAndScale} from '@/utils/themeUtil';
import { onMounted } from 'vue';

onMounted(() => {
    setThemeAndScale();
});

const username = ref("");
const password = ref("");
const showUsernameWarning = ref(false);
const showPasswordWarning = ref(false);
const usernameInput = ref(null);
const passwordInput = ref(null);

let fisrtUsernameInput = true;
let fisrtPasswordInput = true;
let usernameValid = false;
let passwordValid = false;

async function handleLogin() {
  if (!(usernameValid && passwordValid)) {
    if (!usernameValid) {
      showUsernameWarning.value = true;
    }
    if (!passwordValid) {
      showPasswordWarning.value = true;
    }
    // Stop further execution if username or password is empty
    return;
  }
  // Make the HTTP request using Axios
  try {
    const response = await Axios.post(loginUrl, {
      adminName: username.value,
      password: password.value,
    });

    // Handle the response as needed
    if (response.data.loginSuccess === true) {
      // Store the token in the local storage
      localStorage.setItem("token", response.data.token);
      // Redirect to the dashboard by JS
      router.push({ name: "dashboard" });
    } else {
      // Redirect to the error page
      router.push({ name: "accessdenied" });
    }
  } catch (error) {
    // Handle the error
    console.error(error);
  }
}

function updateUsernameValidation() {
  if (!fisrtUsernameInput) {
    if (username.value.trim()) {
      showUsernameWarning.value = false;
      usernameValid = true;
    } else {
      showUsernameWarning.value = true;
      usernameValid = false;
    }
  } else {
    fisrtUsernameInput = false;
    usernameValid = true;
  }
}

function updatePasswordValidation() {
  if (!fisrtPasswordInput) {
    if (password.value.trim()) {
      showPasswordWarning.value = false;
      passwordValid = true;
    } else {
      showPasswordWarning.value = true;
      passwordValid = false;
    }
  } else {
    fisrtPasswordInput = false;
    passwordValid = true;
  }
}

// const checked = ref(false);

// const logoUrl = computed(() => {
//     return `layout/images/${layoutConfig.darkTheme.value ? 'logo-white' : 'logo-dark'}.svg`;
// });
</script>

<template>
  <div
    class="surface-ground flex align-items-center justify-content-center min-h-screen min-w-screen overflow-hidden"
  >
    <div class="flex flex-column align-items-center justify-content-center">
      <div
        style="
          border-radius: 56px;
          padding: 0.3rem;
          background: linear-gradient(
            180deg,
            var(--primary-color) 20%,
            rgb(219, 78, 212) 50%
          );
        "
      >
        <div
          class="w-full surface-card py-8 px-5 sm:px-8"
          style="border-radius: 53px"
        >
          <div class="text-center mb-5">
            <div class="text-900 text-3xl font-medium mb-3">
              Happy to see you here!
            </div>
            <span class="text-600 font-medium">Please sign in to continue</span>
          </div>

          <div>
            <label
              for="username"
              class="block text-900 text-xl font-medium mb-2"
              >Username</label
            >
            <InputText
              ref="usernameInput"
              id="username"
              type="text"
              placeholder="Username"
              class="w-full md:w-30rem mb-0"
              style="padding: 1rem"
              v-model="username"
              @input="updateUsernameValidation"
            />
            <div class="text-red-400 text-right" v-if="showUsernameWarning">
              Username cannot be empty.
            </div>

            <label
              for="password"
              class="block text-900 font-medium text-xl mb-2 mt-2"
              >Password</label
            >
            <Password
              ref="passwordInput"
              id="password"
              v-model="password"
              placeholder="Password"
              toggleMask
              :feedback="false"
              class="w-full mb-0"
              inputClass="w-full"
              :inputStyle="{ padding: '1rem' }"
              @input="updatePasswordValidation"
            ></Password>
            <div class="text-red-400 text-right" v-if="showPasswordWarning">
              Password cannot be empty.
            </div>
            <Button
              label="Submit"
              id="login"
              class="w-full p-3 text-xl mt-5"
              @click="handleLogin"
              @keydown.enter="handleLogin"
              style="
                background: linear-gradient(
                  90deg,
                  rgb(164, 131, 241) 20%,
                  rgb(219, 78, 212) 70%
                );
              "
            ></Button>

            <div class="col-12 mt-4 text-center">
              <div class="text-600 font-medium">Don't have an account?</div>
              <i
                class="pi pi-fw pi-arrow-right text-blue-500 mr-2 mt-3"
                style="vertical-align: center"
              ></i>
              <router-link to="/user/signup" class="text-blue-500"
                >Sign up an account</router-link
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.pi-eye {
  transform: scale(1.6);
  margin-right: 1rem;
}

.pi-eye-slash {
  transform: scale(1.6);
  margin-right: 1rem;
}
</style>
