<template>
    <div>
        <header class="bg-white shadow-sm border-b border-slate-200">
            <nav class="container mx-auto px-6 py-4">
                <div class="flex items-center justify-between">
                    <NuxtLink to="/" class="text-2xl font-bold text-slate-900 hover:text-blue-600 transition-colors">
                        Cinémathèque
                    </NuxtLink>
                    <div class="flex gap-8 items-center">
                        <NuxtLink to="/" class="text-slate-600 hover:text-blue-600 font-medium transition-colors">
                            Accueil
                        </NuxtLink>
                        <NuxtLink to="/personnes" class="text-slate-600 hover:text-blue-600 font-medium transition-colors">
                            Artistes
                        </NuxtLink>
                        <NuxtLink to="/films" class="text-slate-600 hover:text-blue-600 font-medium transition-colors">
                            Films
                        </NuxtLink>
                        <Login />
                    </div>
                </div>
            </nav>
        </header>
        <slot />
    </div>
</template>