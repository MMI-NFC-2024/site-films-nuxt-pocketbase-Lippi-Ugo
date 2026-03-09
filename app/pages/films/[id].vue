<script setup lang="ts">
import ImgPb from '~/components/ImgPb.vue';
import type { FilmResponse, PersonneResponse } from '~/utils/pocketbase-types';

const route = useRoute();
const id = route.params.id as string;
const NuxtApp = useNuxtApp();
const film = await NuxtApp.$pb.collection('Film').getOne<FilmResponse<{
    realisateur: PersonneResponse,
    scenariste: PersonneResponse[],
    producteur: PersonneResponse[]
}>>(id, {
    expand: 'realisateur,scenariste,producteur'
});

const getCountryName = (code: string) => {
    try {
        const regionNames = new Intl.DisplayNames(['fr'], { type: 'region' });
        return regionNames.of(code.toUpperCase());
    } catch {
        return code;
    }
};
</script>

<template>
    <div class="max-w-4xl mx-auto p-4">
        <NuxtLink to="/films" class="inline-flex items-center gap-2 text-blue-600 hover:text-blue-800 mb-6">
            <span>←</span>
            <span>Retour à la liste des films</span>
        </NuxtLink>
        
        <div class="flex gap-8">
            <div class="shrink-0">
                <ImgPb :record="film" filename="affiche" width="300"/>
            </div>
            <div class="flex-1">
                <h1 class="text-4xl font-bold mb-4">{{ film.titre }}</h1>
                
                <div class="space-y-2 mb-4">
                    <p v-if="film.date_sortie">
                        <span class="font-semibold">Date de sortie :</span> {{ new Date(film.date_sortie).getFullYear() }}
                    </p>
                    <p v-if="film.duree_min">
                        <span class="font-semibold">Durée :</span> {{ film.duree_min }} minutes
                    </p>
                    <p v-if="film.pays_origine">
                        <span class="font-semibold">Pays d'origine :</span> {{ getCountryName(film.pays_origine) }}
                    </p>
                    <p v-if="film.genres && film.genres.length">
                        <span class="font-semibold">Genres :</span> {{ film.genres.join(', ') }}
                    </p>
                    <p v-if="film.expand?.realisateur">
                        <span class="font-semibold">Réalisateur :</span> {{ `${film.expand.realisateur.prenom || ''} ${film.expand.realisateur.nom || ''}`.trim() }}
                    </p>
                    <p v-if="film.expand?.scenariste && film.expand.scenariste.length">
                        <span class="font-semibold">Scénariste(s) :</span> {{ film.expand.scenariste.map(p => `${p.prenom || ''} ${p.nom || ''}`.trim()).join(', ') }}
                    </p>
                    <p v-if="film.expand?.producteur && film.expand.producteur.length">
                        <span class="font-semibold">Producteur(s) :</span> {{ film.expand.producteur.map(p => `${p.prenom || ''} ${p.nom || ''}`.trim()).join(', ') }}
                    </p>
                </div>

                <div v-if="film.synopsis" class="mt-6">
                    <h2 class="text-2xl font-semibold mb-2">Synopsis</h2>
                    <p class="text-gray-700">{{ film.synopsis }}</p>
                </div>
            </div>
        </div>
        <NuxtLink :to="{name: 'films-edit-id', params: {id}}">
            Modifier le film
        </NuxtLink>
    </div>
</template>