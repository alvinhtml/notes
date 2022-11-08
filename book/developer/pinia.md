# pinia

Pinia 是 Vue 的存储库，它允许您跨组件/页面共享状态。

## 安装

pinia 使用您最喜欢的包管理器安装：

```
yarn add pinia

# or with npm

npm install pinia
```

创建一个 pinia（根存储）并将其提交给应用程序：

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'

const app = createApp(App)
app.use(createPinia())
```

## 使用

```js
// ./store/userStore.ts

import { defineStore, acceptHMRUpdate } from 'pinia'

// import api service
import { login } from '../api/user'


interface User {
  id: number
  name: string
  email: string
}

interface LoginForm {
  name: string
  password: string
}

export const useUserStore = defineStore('user', {
  state: () => ({
    users: [],
    profile: {},
    logined: false
  }),

  actions: {
    async login(payload: LoginForm) {
      const data = await login(payload)

      if (data.token) {
        window.localStorage.setItem('token', data.token)
      }

      this.$patch({
        logined: true
      })
    }
  }
})

if (import.meta.hot) {
  import.meta.hot.accept(acceptHMRUpdate(useUserStore, import.meta.hot))
}
```

```html
// ./pages/login.vue

<script setup lang="ts">
  import { ref, reactive, onBeforeMount } from 'vue'

  const userStore = useUserStore()

  const formDate = reactive({
    name: 'testuser',
    password: '123456',
  })

  const handleLogin = async () => {
    if (!isLoading.value) {
      isLoading.value = true

      userStore
        .login({
          name: formDate.name,
          password: formDate.password,
        })
        .then(() => {
          // Login successfu
        })
        .catch(() => {
          // Throw error
        })
    }
  }

  onBeforeMount(() => {})
</script>

<template>
  <div class="modern-login">
    <form @submit.prevent="handleLogin">
      <div>
        <input
          type="text"
          class="input"
          placeholder=""
          autocomplete="name"
          v-model="formDate.name"
        />
      </div>
      <div>
        <input
          type="password"
          class="input"
          autocomplete="current-password"
          v-model="formDate.password"
        />
      </div>
      <div>
        <button type="submit">Login</button>
      </div>
    </form>
  </div>
</template>

<style lang="scss" scoped></style>
```
