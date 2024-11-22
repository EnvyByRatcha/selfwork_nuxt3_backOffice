<template>
    <div class="space-x-4  flex items-center">
        <button class="btn bg-white" onclick="modalProduct.showModal()"><svg xmlns="http://www.w3.org/2000/svg"
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
                    <th>Category</th>
                    <th>cost</th>
                    <th>qty</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in products" :key="product.id">
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
            <label class="input input-bordered flex items-center gap-2">
                qty
                <input v-model="product.qty" type="number" class="grow" placeholder="Enter Qty" />
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

const product = ref({
    id: 0, name: '', cost: '', qty: '', categoryId: 1
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
        const res = await axios.get(config.apiPath + '/api/product/list')
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
            qty: product.value.qty,
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

}

const selectProduct = (item) => {
    product.value = item
}

const clearForm = () => {
    product.value.id = 0
    product.value.name = ''
    product.value.cost = ''
    product.value.qty = ''
    product.categoryId = 1
}

definePageMeta({
    layout: 'main'
})
</script>

<style scoped></style>