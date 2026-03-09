<script setup lang="ts">
const film = ref({} as FilmResponse);
const previewUrl = ref<string | null>(null);
const nuxtApp = useNuxtApp();
const route = useRoute();
const id = route.params.id as string;
if (id) {
    const film_temp = await nuxtApp.$pb.collection("Film").getOne(id);
    film.value = {
        ...film_temp,
        date_sortie: film_temp.date_sortie ? new Date(film_temp.date_sortie).toISOString().split('T')[0] : "",
    };
}

async function envoyerFilm() {
    const filmAjouté = id ? (await nuxtApp.$pb.collection("Film").update(id, {
        ...film.value,
        user: nuxtApp.$user.value?.id,
    })) : (await nuxtApp.$pb.collection("Film").create({
        ...film.value,
        user: nuxtApp.$user.value?.id,
    }));
    if (filmAjouté) {
        useRouter().push({ name: "films-id", params: { id: filmAjouté.id } });
    }
}
function createObjectURL(fichier : File) {
    return globalThis.URL.createObjectURL(fichier);
}
</script>

<template>
    <div class="max-w-4xl mx-auto p-6">

        <h1 class="text-3xl font-bold text-gray-800 mb-8 text-center">Édition du Film</h1>
        <form @submit.prevent="envoyerFilm" class="bg-white shadow-lg rounded-lg p-8 space-y-6">
            <div v-if="film.affiche" class="w-48">
                <ImgPb v-if="typeof film.affiche === 'string'" :record="film" :filename="'affiche'" thumb="400x600"
                    class="w-full h-auto rounded-lg shadow-md" />
                <img v-else :src="createObjectURL(film.affiche)" alt="Affiche du film" class="w-full h-auto rounded-lg shadow-md" />
                <button @click.prevent="film.affiche = ''" class="mt-2 text-sm text-red-600 hover:underline">Supprimer
                    l'image</button>
            </div>
            <label v-else>Choisir un fichier
                <input type="file" @change="film.affiche = ($event.target as HTMLInputElement)?.files?.[0]"
                    class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-lg file:border-2 file:border-gray-300 file:text-sm file:font-semibold file:bg-gray-50 hover:file:bg-gray-100 transition-colors">
            </label>
            <div class="space-y-2">
                <label class="block text-sm font-semibold text-gray-700">Titre</label>
                <input type="text" v-model="film.titre"
                    class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all"
                    placeholder="Entrez le titre du film">
            </div>
            <div class="space-y-2">
                <label class="block text-sm font-semibold text-gray-700">Synopsis</label>
                <textarea name="synopsis" v-model="film.synopsis" rows="5"
                    class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all resize-vertical"
                    placeholder="Décrivez le synopsis du film">
                </textarea>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="space-y-2">
                    <label class="block text-sm font-semibold text-gray-700">Date de sortie</label>
                    <input type="date" v-model="film.date_sortie"
                        class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all">
                </div>
                <div class="space-y-2">
                    <label class="block text-sm font-semibold text-gray-700">Durée (min)</label>
                    <input type="number" v-model="film.duree_min" min="1"
                        class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all"
                        placeholder="120">
                </div>
            </div>
            <div class="space-y-2">
                <label class="block text-sm font-semibold text-gray-700">Pays d'origine</label>
                <select v-model="film.pays_origine"
                    class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all bg-white">
                    <option v-for="pays_origine in FilmPaysOrigineOptions" :value="pays_origine">{{ pays_origine }}
                    </option>
                </select>
            </div>
            <div class="space-y-2">
                <label class="block text-sm font-semibold text-gray-700">Genres</label>
                <select multiple v-model="film.genres"
                    class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all bg-white min-h-[120px]">
                    <option v-for="genres in FilmGenresOptions" :value="genres" class="py-1">{{ genres }}</option>
                </select>
            </div>
            <div>
                <button
                    class="mt-6 w-full md:w-auto bg-blue-600 text-white font-semibold py-3 px-6 rounded-lg hover:bg-blue-700 transition-colors">
                    Enregistrer les modifications
                </button>
            </div>
        </form>
    </div>
</template>