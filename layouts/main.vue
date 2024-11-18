<template>

    <div class="bg-[#F5F6FA] w-screen h-screen flex">
        <div class="h-screen bg-white w-48 shadow-lg flex flex-col">
            <div class="text-center py-4">
                <h2 class="font-bold text-xl text-blue-700">Backoffice
                </h2>
            </div>

            <div>
                <ul class="px-4 space-y-4">
                    <li class="hover:text-white hover:bg-[#4880FF] px-4 py-2 rounded-md">
                        <NuxtLink class="block " to="/">
                            Dashboard
                        </NuxtLink>
                    </li>
                    <li class="hover:text-white hover:bg-[#4880FF] px-4 py-2 rounded-md">
                        <NuxtLink class="block" to="/">
                            User
                        </NuxtLink>
                    </li>
                    <li class="hover:text-white hover:bg-[#4880FF] px-4 py-2 rounded-md">
                        <NuxtLink class="block" to="/">
                            Workplace
                        </NuxtLink>
                    </li>
                    <li class="hover:text-white hover:bg-[#4880FF] px-4 py-2 rounded-md">
                        <NuxtLink class="block" to="/">
                            Store
                        </NuxtLink>
                    </li>
                </ul>

            </div>
        </div>
        <div class="flex flex-col w-full">
            <div class="py-4 px-4 shadow-lg bg-white">nav</div>
            <div class="p-4">
                <slot />
            </div>

        </div>
    </div>

</template>

<script>

</script>

<style scoped>

</style>
