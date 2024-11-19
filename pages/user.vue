<template>
    <div class="space-x-4  flex items-center">
        <button class="btn bg-white" onclick="modalUser.showModal()"><svg xmlns="http://www.w3.org/2000/svg" fill="none"
                viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
            </svg>
        </button>
        <button @click="fetchDataByRole('USER')" class="btn bg-white">USER</button>
        <button @click="fetchDataByRole('ADMIN')" class="btn bg-white">ADMIN</button>
        <button @click="fetchData" class="btn bg-white">USER & ADMIN</button>
    </div>

    <div class="bg-white p-4 shadow-lg rounded-lg mt-4">
        <table class="table">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Username</th>
                    <th>Role</th>
                    <th>Status</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in users" :key="user.id">
                    <td>{{ user.name }}</td>
                    <td>{{ user.username }}</td>
                    <td>{{ user.role }}</td>
                    <td>{{ user.status }}</td>
                    <td class="space-x-2 text-center">
                        <button onclick="modalUser.showModal()" @click="selectUser(user)"
                            class="btn btn-info btn-sm"><svg xmlns="http://www.w3.org/2000/svg" fill="none"
                                viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                                <path stroke-linecap="round" stroke-linejoin="round"
                                    d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L10.582 16.07a4.5 4.5 0 0 1-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 0 1 1.13-1.897l8.932-8.931Zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0 1 15.75 21H5.25A2.25 2.25 0 0 1 3 18.75V8.25A2.25 2.25 0 0 1 5.25 6H10" />
                            </svg>

                        </button>
                        <button @click="remove(user)" class="btn btn-error btn-sm"><svg
                                xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                                stroke="currentColor" class="size-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12" />
                            </svg>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>

    </div>

    <ModalComponent target="modalUser" header="New Employee" @close="clearForm">
        <form @submit.prevent="save" class="mt-4 space-y-4">
            <label class="input input-bordered flex items-center gap-2">
                Name
                <input v-model="name" type="text" class="grow" placeholder="Name" />
            </label>
            <label class="input input-bordered flex items-center gap-2">
                Username
                <input v-model="username" type="text" class="grow" placeholder="admin@selfwork.com" />
            </label>
            <label class="input input-bordered flex items-center gap-2">
                Password
                <input v-model="password" type="password" class="grow" />
            </label>
            <select v-model="role" class="select select-error w-full max-w-xs">
                <option>ADMIN</option>
                <option>USER</option>

            </select>
            <br>
            <button type="submit" class="btn btn-success">Save</button>
        </form>
    </ModalComponent>

</template>

<script setup>
import axios from 'axios';
import config from '../config'
import { ref } from 'vue';
import Swal from 'sweetalert2';

const users = ref([])

const id = ref(0);
const name = ref('');
const username = ref('');
const password = ref('');
const role = ref('USER');

onMounted(() => {
    fetchData()
})

const fetchData = async () => {
    try {
        const res = await axios.get(config.apiPath + '/api/user/list/all')
        users.value = res.data.results
    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.message,
            icon: 'error'
        })
    }
}

const fetchDataByRole = async (role) => {
    try {
        const res = await axios.get(config.apiPath + '/api/user/list/' + role)
        users.value = res.data.results
    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.message,
            icon: 'error'
        })
    }
}

const save = async () => {
    try {
        const payload = {
            name: name.value,
            username: username.value,
            password: password.value,
            role: role.value,
            id: id.value
        }

        let res;
        if (id.value == 0) {
            res = await axios.post(config.apiPath + '/api/user/create', payload)
            if (res.data.message == 'success') {
                fetchData()
                document.getElementById('modalUser_btnClose').click();
                Swal.fire({
                    title: 'Create user',
                    text: 'Create user success',
                    icon: 'success',
                    timer: 1500
                })
            }
        } else {
            res = await axios.put(config.apiPath + '/api/user/update', payload)
            if (res.data.message == 'success') {
                fetchData()
                document.getElementById('modalUser_btnClose').click();
                Swal.fire({
                    title: 'Create user',
                    text: 'Create user success',
                    icon: 'success',
                    timer: 1500
                })
            }
        }
    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.message,
            icon: 'error'
        })
    }
}

const remove = async (user) => {
    try {
        const button = await Swal.fire({
            title: 'Remove',
            text: 'Confirm remove ' + user.name + ' ??',
            icon: 'question',
            showConfirmButton: true,
            showCancelButton: true
        })

        if (button.isConfirmed) {
            const res = await axios.delete(config.apiPath + '/api/user/remove/' + user.id)
            if (res.data.message == 'success') {
                fetchData()
                Swal.fire({
                    title: 'Remove',
                    text: 'Remove success',
                    icon: 'success',
                    timer: 1500
                })
            }
        }

    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.message,
            icon: 'error'
        })
    }
}

const selectUser = (user) => {
    id.value = user.id;
    name.value = user.name;
    username.value = user.username;
    password.value = user.password;
    role.value = user.role;
}

const clearForm = () => {
    id.value = 0;
    name.value = '';
    username.value = '';
    password.value = '';
    role.value = 'USER';
}

definePageMeta({
    layout: 'main'
})

</script>

<style scoped></style>