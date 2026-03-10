<script setup lang="ts">
import ImgPb from '~/components/ImgPb.vue';
import type { PersonneResponse } from '~/utils/pocketbase-types';

const route = useRoute();
const id = route.params.id as string;
const NuxtApp = useNuxtApp();
const personne = await NuxtApp.$pb.collection('Personne').getOne<PersonneResponse>(id);

const getNationalite = (code: string) => {
    const nationalites: Record<string, string> = {
        'FR': 'Français',
        'GB': 'Britannique',
        'US': 'Américain',
        'CA': 'Canadien',
        'AT': 'Autrichien',
    };
    return nationalites[code.toUpperCase()] || code;
};

const formatDate = (date: string) => {
    return new Date(date).toLocaleDateString('fr-FR', { year: 'numeric', month: 'long', day: 'numeric' });
};

const calculerAge = () => {
    if (!personne.date_naissance) return null;

    const dateNaissance = new Date(personne.date_naissance);
    const dateFin = personne.date_deces ? new Date(personne.date_deces) : new Date();

    let age = dateFin.getFullYear() - dateNaissance.getFullYear();
    const moisDiff = dateFin.getMonth() - dateNaissance.getMonth();

    if (moisDiff < 0 || (moisDiff === 0 && dateFin.getDate() < dateNaissance.getDate())) {
        age--;
    }

    return age;
};
</script>

<template>
    <div class="max-w-4xl mx-auto p-4">
        <NuxtLink to="/personnes" class="inline-flex items-center gap-2 text-blue-600 hover:text-blue-800 mb-6">
            <span>←</span>
            <span>Retour à la liste des personnes</span>
        </NuxtLink>

        <div class="flex gap-8">
            <div class="shrink-0">
                <ImgPb :record="personne" filename="photo" width="300" />
            </div>
            <div class="flex-1">
                <h1 class="text-4xl font-bold mb-4">{{ personne.prenom }} {{ personne.nom }}</h1>

                <div class="space-y-2 mb-4">
                    <p v-if="personne.date_naissance">
                        <span class="font-semibold">Date de naissance :</span> {{ formatDate(personne.date_naissance) }}
                        <span v-if="!personne.date_deces && calculerAge()" class="text-gray-600"> ({{ calculerAge() }}
                            ans)</span>
                    </p>
                    <p v-if="personne.date_deces">
                        <span class="font-semibold">Date de décès :</span> {{ formatDate(personne.date_deces) }}
                        <span v-if="calculerAge()" class="text-gray-600"> ({{ calculerAge() }} ans)</span>
                    </p>
                    <p v-if="personne.nationalite">
                        <span class="font-semibold">Nationalité :</span> {{ getNationalite(personne.nationalite) }}
                    </p>
                    <p v-if="personne.profession && personne.profession.length">
                        <span class="font-semibold">Profession(s) :</span> {{ personne.profession.join(', ') }}
                    </p>
                </div>
            </div>
        </div>
        <NuxtLink :to="{ name: 'personnes-edit-id', params: { id } }">
            Modifier la personne
        </NuxtLink>
    </div>
</template>