<template>
    <div class="space-x-4  flex items-center">
        <button class="btn bg-white" onclick="modalProduct.showModal()"><svg xmlns="http://www.w3.org/2000/svg"
                fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
            </svg>
        </button>
    </div>

    <div class="mt-4">
        <form @submit.prevent="search" class="flex space-x-4">
            <label class="input input-bordered flex items-center gap-2">
                <input type="text" class="grow" placeholder="Enter Serial Number" />
            </label>
            <select class="select select-error w-full max-w-xs" required>
                <option v-for="item in categories" :key="item.id" :value="item.id">{{ item.name }}
                </option>
            </select>
            <button type="submit" class="btn btn-success"><svg xmlns="http://www.w3.org/2000/svg" fill="none"
                    viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                    <path stroke-linecap="round" stroke-linejoin="round"
                        d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z" />
                </svg>
            </button>
        </form>
    </div>

    <div class="bg-white p-4 shadow-lg rounded-lg mt-4">
        <table class="table">
            <thead>
                <tr>
                    <th>Serial Number</th>
                    <th>Category</th>
                    <th>Status</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in productDetails" :key="item.id">
                    <td>{{ item.serialNumber }}</td>
                    <td>{{ item.Product.Category.name }}</td>
                    <td>{{ item.status }}</td>
                    <td class="space-x-2 text-center">
                        <button onclick="modalProduct.showModal()" @click="selectProductDetail(item)"
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
                    <th></th>
                </tr>
            </tbody>
        </table>
    </div>

    <ModalComponent target="modalProduct" header="Add Product" @close="clearForm">
        <form @submit.prevent="save" class="mt-4 space-y-4">
            <select v-model="productDetail.productId" class="select select-success w-full max-w-xs" required>
                <option v-for="item in products" :key="item.id" :value="item.id">{{ item.name }}
                </option>
            </select>
            <label class="input input-bordered flex items-center gap-2">
                Serial Number
                <input v-model="productDetail.serialNumber" type="text" class="grow"
                    placeholder="Enter Serial Number" required>
            </label>
            <button type="submit" class="btn btn-success">Save</button>
        </form>
    </ModalComponent>

</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import config from '../../config'
import Swal from 'sweetalert2';

const products = ref([])
const categories = ref([])
const productDetails = ref([])

const productDetail = ref({
    id: 0,
    serialNumber: '',
    productId: 0,
})

onMounted(() => {
    fetchDataCategory()
    fetchDataProduct()
    fetchDataProductDetail()
})

const fetchDataCategory = async () => {
    try {
        const res = await axios.get(config.apiPath + '/api/category/list')
        categories.value = res.data.results
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

const fetchDataProductDetail = async () => {
    try {
        const res = await axios.get(config.apiPath + '/api/productDetail/list')
        productDetails.value = res.data.results
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
            id: productDetail.value.id,
            serialNumber: productDetail.value.serialNumber,
            productId: productDetail.value.productId
        }

        let res
        if (productDetail.value.id == 0) {
            res = await axios.post(config.apiPath + '/api/productDetail/create', payload)
            if (res.data.message == 'success') {
                Swal.fire({
                    title: 'Create Product',
                    text: 'Success',
                    icon: 'success',
                    timer: 1200
                })

                document.getElementById('modalProduct_btnClose').click();
                fetchDataProductDetail()
            }
        } else {
            res = await axios.put(config.apiPath + '/api/productDetail/update', payload)
            if (res.data.message == 'success') {
                Swal.fire({
                    title: 'Update Product',
                    text: 'Success',
                    icon: 'success',
                    timer: 1200
                })

                document.getElementById('modalProduct_btnClose').click();
                fetchDataProductDetail()
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

const remove = async () => {

}

const search = async () => {

}

const selectProductDetail = (item) => {
    productDetail.value.id = item.id
    productDetail.value.serialNumber = item.serialNumber
    productDetail.value.productId = item.productId
}

const clearForm = () => {
    productDetail.value.id = 0
    productDetail.value.serialNumber = ''
    productDetail.value.productId = 0
}



definePageMeta({
    layout: 'main'
})
</script>

<style scoped></style>