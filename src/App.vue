<template>
    <div class="min-h-screen bg-gray-100 flex justify-center p-6">
        <div class="w-full max-w-2xl">

            <h1 class="text-3xl font-bold text-gray-800 mb-6 text-center">
                My App
            </h1>

            <div class="space-y-4">
                <div v-for="user in users" :key="user.id"
                    class="bg-white rounded-2xl shadow-md p-5 hover:shadow-lg transition">
                    <h1 class="text-lg font-semibold text-gray-900">
                        {{ user.name }}
                    </h1>

                    <p class="text-gray-600 mt-2 leading-relaxed">
                        {{ user.message }}
                    </p>
                </div>
            </div>

        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'

const users = ref([
    { id: 1, name: 'HoHo', message: 'ok' },
    { id: 2, name: 'HoHo', message: 'ok' },
    { id: 3, name: 'HoHo', message: 'ok' },
])

const subject = ref([
    { id: 1, name: 'He he', }
])
</script>