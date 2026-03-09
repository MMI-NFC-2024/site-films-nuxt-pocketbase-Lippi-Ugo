<template>
    <div>
        <nav>
            <NuxtLink to="/">
                Accueil
            </NuxtLink>
            <NuxtLink to="/films">
                Films
            </NuxtLink>
            <Login />
        </nav>
        <slot />
    </div>
</template>