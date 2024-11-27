<template>
    <div class="space-x-4  flex items-center">
        <button class="btn bg-white" onclick="modalProduct.showModal()"><svg xmlns="http://www.w3.org/2000/svg"
                fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
            </svg>
        </button>
        <h2 class="text-2xl">Product</h2>
    </div>

    <div class="mt-4 flex space-x-4">
        <select v-model="searchCategoryId" class="select select-sm select-primary w-full max-w-xs">
            <option :value="0">All</option>
            <option v-for="item in categorys" :key="item.id" :value="item.id">{{ item.name }}</option>
        </select>
        <button @click="fetchDataProduct" type="submit" class="btn btn-primary btn-sm"><svg
                xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                stroke="currentColor" class="size-6">
                <path stroke-linecap="round" stroke-linejoin="round"
                    d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z" />
            </svg>
        </button>
    </div>

    <div class="bg-white p-4 shadow-lg rounded-lg mt-4">
        <table class="table">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>cost</th>
                    <th>qty</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in products" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.Category.name }}</td>
                    <td>{{ item.cost }}</td>
                    <td>{{ item.qty }}</td>
                    <td class="space-x-2 text-center">
                        <button onclick="modalProduct.showModal()" @click="selectProduct(item)"
                            class="btn btn-info btn-sm"><svg xmlns="http://www.w3.org/2000/svg" fill="none"
                                viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                                <path stroke-linecap="round" stroke-linejoin="round"
                                    d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L10.582 16.07a4.5 4.5 0 0 1-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 0 1 1.13-1.897l8.932-8.931Zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0 1 15.75 21H5.25A2.25 2.25 0 0 1 3 18.75V8.25A2.25 2.25 0 0 1 5.25 6H10" />
                            </svg>

                        </button>
                        <NuxtLink :to="`/product/${item.name.replaceAll(' ', '-')}`" class="btn btn-warning btn-sm"><svg
                                xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                                stroke="currentColor" class="size-6">
                                <path stroke-linecap="round" stroke-linejoin="round"
                                    d="M9 12h3.75M9 15h3.75M9 18h3.75m3 .75H18a2.25 2.25 0 0 0 2.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 0 0-1.123-.08m-5.801 0c-.065.21-.1.433-.1.664 0 .414.336.75.75.75h4.5a.75.75 0 0 0 .75-.75 2.25 2.25 0 0 0-.1-.664m-5.8 0A2.251 2.251 0 0 1 13.5 2.25H15c1.012 0 1.867.668 2.15 1.586m-5.8 0c-.376.023-.75.05-1.124.08C9.095 4.01 8.25 4.973 8.25 6.108V8.25m0 0H4.875c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V9.375c0-.621-.504-1.125-1.125-1.125H8.25ZM6.75 12h.008v.008H6.75V12Zm0 3h.008v.008H6.75V15Zm0 3h.008v.008H6.75V18Z" />
                            </svg>
                        </NuxtLink>
                        <button @click="remove(item)" class="btn btn-error btn-sm"><svg
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

    <ModalComponent target="modalProduct" header="New Product" @close="clearForm">
        <form @submit.prevent="save" class="mt-4 space-y-4">
            <label class="input input-bordered flex items-center gap-2">
                Product Name
                <input v-model="product.name" type="text" class="grow" placeholder="Enter Product Name" />
            </label>
            <label class="input input-bordered flex items-center gap-2">
                Cost
                <input v-model="product.cost" type="number" class="grow" placeholder="Enter Cost" />
            </label>

            <select v-model="product.categoryId" class="select select-error w-full max-w-xs" required>
                <option v-for="category in categorys" :key="category.id" :value="category.id">{{ category.name }}
                </option>
            </select>

            <br>
            <button type="submit" class="btn btn-success">Save</button>
        </form>
    </ModalComponent>
</template>

<script setup>
import axios from 'axios';
import Swal from 'sweetalert2';
import config from "../../config"
import { ref } from 'vue';

const categorys = ref([])
const products = ref([])

const searchCategoryId = ref(0)
const product = ref({
    id: 0, name: '', cost: 0, categoryId: 1
})

onMounted(() => {
    fetchDataCategory()
    fetchDataProduct()
})

const fetchDataCategory = async () => {
    try {
        const res = await axios.get(config.apiPath + '/api/category/list')
        categorys.value = res.data.results
    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.messge,
            icon: 'error'
        })
    }
}

const fetchDataProduct = async () => {
    try {
        const res = await axios.get(config.apiPath + '/api/product/list/' + searchCategoryId.value)
        products.value = res.data.results
    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.messge,
            icon: 'error'
        })
    }
}


const save = async () => {
    try {
        const payload = {
            id: product.value.id,
            name: product.value.name,
            cost: product.value.cost,
            categoryId: product.value.categoryId
        }

        let res
        if (product.value.id == 0) {
            res = await axios.post(config.apiPath + '/api/product/create', payload)
            if (res.data.message == 'success') {
                Swal.fire({
                    title: 'Create Product',
                    text: 'Success',
                    icon: 'success',
                    timer: 1200
                })

                document.getElementById('modalProduct_btnClose').click();
                fetchDataProduct()
            }
        } else {
            res = await axios.put(config.apiPath + '/api/product/update', payload)
            if (res.data.message == 'success') {
                Swal.fire({
                    title: 'Update Product',
                    text: 'Success',
                    icon: 'success',
                    timer: 1200
                })

                document.getElementById('modalProduct_btnClose').click();
                fetchDataProduct()
            }
        }
    } catch (e) {
        console.log(e)
        Swal.fire({
            title: 'error',
            text: e.messge,
            icon: 'error'
        })
    }
}

const remove = async (product) => {
    try {
        const button = await Swal.fire({
            title: 'Remove Product',
            text: 'Confirm remove ' + product.name + ' ??',
            icon: 'question',
            showConfirmButton: true,
            showCancelButton: true
        })

        if (button.isConfirmed) {
            const res = await axios.delete(config.apiPath + '/api/product/remove/' + product.id)
            if (res.data.message == 'success') {
                Swal.fire({
                    title: 'Remove Product',
                    text: 'success',
                    icon: 'success',
                    timer: 1500
                })

                fetchDataProduct()
            }
        }
    } catch (e) {
        Swal.fire({
            title: 'error',
            text: e.messge,
            icon: 'error'
        })

    }
}

const selectProduct = (item) => {
    product.value.id = item.id
    product.value.name = item.name
    product.value.cost = item.cost
    product.value.categoryId = item.categoryId
}

const clearForm = () => {
    product.value.id = 0
    product.value.name = ''
    product.value.cost = 0
    product.categoryId = 1
}

definePageMeta({
    layout: 'main'
})
</script>

<style scoped></style>