<script setup lang="ts">
const props = defineProps<{
    personne: PersonneResponse;
}>();
</script>

<template>
    <NuxtLink :to="{ name: 'personnes-id', params: { id: personne.id } }" class="text-blue-600 hover:underline">
        {{ personne.prenom }} {{ personne.nom }}
    </NuxtLink>
</template>