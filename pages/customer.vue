<template>
    <div class="space-x-4  flex items-center">
        <button class="btn bg-white" onclick="modalCustomer.showModal()"><svg xmlns="http://www.w3.org/2000/svg"
                fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
            </svg>
        </button>
    </div>

    <div class="bg-white p-4 shadow-lg rounded-lg mt-4">
        <table class="table">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Code</th>
                    <th>Address</th>
                    <th>Email</th>
                    <th>Contact_1</th>
                    <th>Contact_2</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="customer in customers" :key="customer.id">
                    <td>{{ customer.name }}</td>
                    <td>{{ customer.code }}</td>
                    <td>{{ customer.address }}</td>
                    <td>{{ customer.email }}</td>
                    <td>{{ customer.contact1 }}</td>
                    <td>{{ customer.contact2 == '' ? '-' : customer.contact2 }}</td>
                    <td class="text-center space-x-2">
                        <button onclick="modalCustomer.showModal()" @click="selectCustomer(customer)" class="btn btn-info btn-sm"><svg
                                xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                                stroke="currentColor" class="size-6">
                                <path stroke-linecap="round" stroke-linejoin="round"
                                    d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L10.582 16.07a4.5 4.5 0 0 1-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 0 1 1.13-1.897l8.932-8.931Zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0 1 15.75 21H5.25A2.25 2.25 0 0 1 3 18.75V8.25A2.25 2.25 0 0 1 5.25 6H10" />
                            </svg>

                        </button>
                        <button @click="remove(customer)" class="btn btn-error btn-sm"><svg
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

    <ModalComponent target="modalCustomer" header="New Employee" @close="clearForm">
        <form @submit.prevent="save" class="mt-4 space-y-4">
            <label class="input input-bordered flex items-center gap-2">
                Name
                <input v-model="name" type="text" class="grow" placeholder="Name" />
            </label>
            <label class="input input-bordered flex items-center gap-2">
                Code
                <input v-model="code" type="text" class="grow" placeholder="Code" />
            </label>
            <label class="input input-bordered flex items-center gap-2">
                Address
                <input v-model="address" type="text" class="grow" placeholder="Address" />
            </label>
            <label class="input input-bordered flex items-center gap-2">
                Email
                <input v-model="email" type="email" class="grow" placeholder="email@address.com" />
            </label>
            <div class="grid grid-cols-2 gap-2">
                <label class="input input-bordered flex items-center gap-2">
                    Contact
                    <input v-model="contact1" type="tel" class="grow" />
                </label><label class="input input-bordered flex items-center gap-2">
                    Contact
                    <input v-model="contact2" type="tel" class="grow" />
                </label>
            </div>


            <button type="submit" class="btn btn-success">Save</button>
        </form>
    </ModalComponent>
</template>

<script setup>
import axios from 'axios';
import Swal from 'sweetalert2';
import { ref } from 'vue';
import config from '~/config';


const customers = ref([]);

const id = ref(0)
const name = ref('')
const code = ref('')
const address = ref('')
const email = ref('')
const contact1 = ref('')
const contact2 = ref('')

onMounted(() => {
    fetchData()
})

const fetchData = async () => {
    try {
        const res = await axios.get(config.apiPath + '/api/customer/list')
        customers.value = res.data.results
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
            id: id.value,
            name: name.value,
            code: code.value,
            address: address.value,
            email: email.value,
            contact1: contact1.value,
            contact2: contact2.value
        }

        let res
        if (id.value == 0) {
            res = await axios.post(config.apiPath + '/api/customer/create', payload)
            if (res.data.message == 'success') {
                fetchData();
                document.getElementById('modalCustomer_btnClose').click()
                Swal.fire({
                    title: 'Create new customer',
                    text: 'Success',
                    icon: 'success',
                    timer: 1500
                })
            }
        } else {
            res = await axios.put(config.apiPath + '/api/customer/update', payload)
            if (res.data.message == 'success') {
                fetchData();
                document.getElementById('modalCustomer_btnClose').click()
                Swal.fire({
                    title: 'Update customer',
                    text: 'Success',
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

const remove = async (customer) => {
    try {
        const button = await Swal.fire({
            title: 'Remove customer',
            text: 'Confirm remove ' + customer.name + '??',
            icon: 'question',
            showConfirmButton: true,
            showCancelButton: true
        })

        if (button.isConfirmed) {
            const res = await axios.delete(config.apiPath + '/api/customer/remove/' + customer.id)
            if (res.data.message == 'success') {
                fetchData()
                Swal.fire({
                    title: 'Remove customer',
                    text: 'Success',
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

const selectCustomer = (customer) => {
    id.value = customer.id
    name.value = customer.name
    code.value = customer.code
    address.value = customer.address
    email.value = customer.email
    contact1.value = customer.contact1
    contact2.value = customer.contact2
}

const clearForm = () => {
    id.value = 0
    name.value = ''
    code.value = ''
    address.value = ''
    email.value = ''
    contact1.value = ''
    contact2.value = ''
}

definePageMeta({
    layout: 'main'
})
</script>

<style scoped></style>