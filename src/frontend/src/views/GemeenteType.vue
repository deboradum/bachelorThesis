<script setup>
import { ref } from 'vue';
import { useRouter, useRoute } from 'vue-router'

import GemeenteTypeCard from "../components/GemeenteTypeCard.vue"

const route = useRoute()
const router = useRouter()

defineProps({
    fullPath: String,
})

const gemeenteYears = ref([]);

fetch(`http://${import.meta.env.VITE_API_URL}/api/gemeenteYears?gemeente=${route.params.gemeenteName}&meetingType=${route.params.gemeenteType}`)
    .then(response => response.json())
    .then(data => {
        gemeenteYears.value = data.years;
        gemeenteYears.value.sort((a, b) => {
            return parseInt(a.year) - parseInt(b.year);
        });
    })
    .catch(error => {
        console.error('Error fetching data:', error);
    });


function goToGemeenteYear(gemeente, type, year) {
    router.push(`/gemeente/${gemeente}/${type}/${year}`)
}

</script>

<template>
    <div class="flex justify-start flex-col">
        <h1 class="text-5xl m-5 "> {{ route.params.gemeenteName }} {{ route.params.gemeenteType }} </h1>
        <div class="flex flex-col text-left">
            <span v-if="gemeenteYears.length == 0">
                No Meeting years found!
            </span>
            <GemeenteTypeCard v-for="y in gemeenteYears" :gemeente="route.params.gemeenteName" :type="y.year"
                class="hover: cursor-pointer"
                @click="goToGemeenteYear(route.params.gemeenteName, route.params.gemeenteType, y.year)" />
        </div>
    </div>
</template>
