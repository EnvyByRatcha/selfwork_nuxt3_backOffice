<template>
    <div class="bg-slate-200 h-screen w-screen flex items-center justify-center">
        <div class="bg-white shadow-lg p-4 rounded-lg w-[360px]">
            <h2 class="text-xl font-bold text-blue-700 w-full text-center">Self@Work Backoffice</h2>
            <form @submit.prevent="signIn()" class="mt-4">
                <div class="flex flex-col gap-2">
                    <input v-model="username" class="py-1 px-2 round border border-gray-300 rounded-sm" type="email"
                        required placeholder="Email address" />
                    <input v-model="password" class="py-1 px-2 round border border-gray-300 rounded-sm" type="password"
                        required placeholder="Password">
                </div>
                <button type="submit"
                    class="mt-4 bg-blue-700 hover:bg-blue-800 rounded-md text-white font-bold py-1 text-center w-full">Login</button>
            </form>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import config from '../config'

const username = ref('')
const password = ref('')

async function signIn() {
    try {
        const payload = {
            username: username.value,
            password: password.value
        }

        const res = await axios.post(config.apiPath + '/api/user/signIn', payload)
        if (res.data) {
            localStorage.setItem('token', res.data.token)
            localStorage.setItem('name', res.data.name)
            localStorage.setItem('id', res.data.id)

            navigateTo('/dashboard')
        }
    } catch (e) {
        console.log(e)
    }
}
</script>

<style scoped></style>