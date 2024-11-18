<template>
    <dialog :id="target" class="modal">
        <div class="modal-box">
            <form method="dialog">
                <button :id="target + '_btnClose'"
                    class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">✕</button>
            </form>
            <h3 class="text-lg font-bold">{{ header }}</h3>
            <slot />
        </div>
    </dialog>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
    target: {
        type: String,
        required: true
    },
    header: {
        type: String,
        required: true
    }
})
</script>

<style scoped></style>