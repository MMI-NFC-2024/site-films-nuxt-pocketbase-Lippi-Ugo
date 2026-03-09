<script setup lang="ts">
const nuxtApp = useNuxtApp();
async function loginGitHub() {
    const authdata = await nuxtApp.$pb.collection("users").authWithOAuth2({
        provider: "github",
    });
    console.log("Login réussi : ", authdata);
}
const user = nuxtApp.$user;
</script>

<template>
    <button v-if="user" @click="nuxtApp.$pb.authStore.clear()">Se Déconnecter {{ user.name || user.email }}</button>
    <button v-else @click="loginGitHub">Se Connecter avec GitHub</button>
</template>