<template>
  <q-page padding>
    <q-btn @click="access">Ingresar</q-btn>
    <q-btn @click="createLink">Crear Link</q-btn>
    {{ token }} - {{ expiresIn }}
  </q-page>
</template>

<script setup>
import { api } from 'src/boot/axios';
import { ref } from 'vue';

const token = ref('')
const expiresIn = ref('')

const access = async () => {
  try {
    const res = await api.post('/auth/login', {
      email: "tomasw300@gmail.com",
      password: "1234567"
    })
    token.value = res.data.token
    expiresIn.value = res.data.expiresIn
    setTime()
  } catch (error) {
    console.log(error);
  }
}

const createLink = async () => {
  try {
    const res = await api({
      method: 'POST',
      url: '/links',
      headers: {
        'Authorization': 'Bearer ' + token.value
      },
      data: {
        longLink: "https://www.youtube.com/watch?v=VrLvbzHVT9A&t=15643s&ab_channel=Bluuweb"
      }
    })
    console.log(res.data);
  } catch (error) {
    console.log(error);
  }
}

const setTime = () => {
  setTimeout(() => {
    refreshToken()
  }, expiresIn.value * 1000 - 6000)
}

const refreshToken = async () => {
  try {
    const res = await api.get('/auth/refresh')
    token.value = res.data.token
    expiresIn.value = res.data.expiresIn
    setTime()
  } catch (error) {
    console.log(error);
  }
}
refreshToken()



</script>
