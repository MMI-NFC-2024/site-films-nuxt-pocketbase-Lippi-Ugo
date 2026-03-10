<script setup lang="ts">
const personne = ref({} as PersonneResponse);
const nuxtApp = useNuxtApp();
const route = useRoute();
const id = route.params.id as string;
if (id) {
    const personne_temp = await nuxtApp.$pb.collection("Personne").getOne(id);
    personne.value = {
        ...personne_temp,
        date_naissance: personne_temp.date_naissance ? new Date(personne_temp.date_naissance).toISOString().split('T')[0] : undefined,
        date_deces: personne_temp.date_deces ? new Date(personne_temp.date_deces).toISOString().split('T')[0] : undefined,
    } as any;
}

async function envoyerPersonne() {
    const personneAjoutée = id ? (await nuxtApp.$pb.collection("Personne").update(id, {
        ...personne.value,
        user: nuxtApp.$user.value?.id,
    })) : (await nuxtApp.$pb.collection("Personne").create({
        ...personne.value,
        user: nuxtApp.$user.value?.id,
    }));
    if (personneAjoutée) {
        useRouter().push({ name: "personnes-id", params: { id: personneAjoutée.id } });
    }
}
function createObjectURL(fichier: File) {
    return globalThis.URL.createObjectURL(fichier);
}
</script>

<template>
    <div class="max-w-4xl mx-auto p-6">

        <h1 class="text-3xl font-bold text-gray-800 mb-8 text-center">Édition de la Personne</h1>
        <form @submit.prevent="envoyerPersonne" class="bg-white shadow-lg rounded-lg p-8 space-y-6">
            <div v-if="personne.photo" class="w-48">
                <ImgPb v-if="typeof personne.photo === 'string'" :record="personne" :filename="'photo'" thumb="400x600"
                    class="w-full h-auto rounded-lg shadow-md" />
                <img v-else :src="createObjectURL(personne.photo as any)" alt="Photo de la personne"
                    class="w-full h-auto rounded-lg shadow-md" />
                <button @click.prevent="personne.photo = undefined as any"
                    class="mt-2 text-sm text-red-600 hover:underline">Supprimer
                    l'image</button>
            </div>
            <label v-else>Choisir un fichier
                <input type="file" @change="personne.photo = ($event.target as HTMLInputElement)?.files?.[0] as any"
                    class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-lg file:border-2 file:border-gray-300 file:text-sm file:font-semibold file:bg-gray-50 hover:file:bg-gray-100 transition-colors">
            </label>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="space-y-2">
                    <label class="block text-sm font-semibold text-gray-700">Nom</label>
                    <input type="text" v-model="personne.nom"
                        class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all"
                        placeholder="Entrez le nom">
                </div>
                <div class="space-y-2">
                    <label class="block text-sm font-semibold text-gray-700">Prénom</label>
                    <input type="text" v-model="personne.prenom"
                        class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all"
                        placeholder="Entrez le prénom">
                </div>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="space-y-2">
                    <label class="block text-sm font-semibold text-gray-700">Date de naissance</label>
                    <input type="date" v-model="personne.date_naissance"
                        class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all">
                </div>
                <div class="space-y-2">
                    <label class="block text-sm font-semibold text-gray-700">Date de décès</label>
                    <input type="date" v-model="personne.date_deces"
                        class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all">
                </div>
            </div>
            <div class="space-y-2">
                <label class="block text-sm font-semibold text-gray-700">Nationalité</label>
                <select v-model="personne.nationalite"
                    class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all bg-white">
                    <option v-for="nationalite in PersonneNationaliteOptions" :value="nationalite">{{ nationalite }}
                    </option>
                </select>
            </div>
            <div class="space-y-2">
                <label class="block text-sm font-semibold text-gray-700">Profession(s)</label>
                <select multiple v-model="personne.profession"
                    class="w-full px-4 py-2 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all bg-white min-h-30">
                    <option v-for="profession in PersonneProfessionOptions" :value="profession" class="py-1">{{
                        profession }}</option>
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