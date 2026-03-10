<script setup lang="ts">

const props = defineProps<{
  /** L'enregistrement 'complet' (id et collectionId ou collectionName) de PocketBase */
  record: BaseSystemFields;
  // record: {
  //   id: string;
  // } & (
  //   | {
  //       collectionId: string;
  //     }
  //   | {
  //       collectionName: string;
  //     }
  // );
  /** La valeur du nom de fichier stocké dans la colonne de PocketBase */
  filename: string;
  /** La largeur aussi utilisée pour fixer la largeur d'image demandée à PocketBase */
  width?: number | string;
  /** La hauteur aussi utilisée pour fixer la hauteur d'image demandée à PocketBase */
  height?: number | string;
  /** Pour fixer manuellement les paramétres de redimensitionnement de PocketBase \
   * https://pocketbase.io/docs/files-handling/#file-url
   */
  thumb?: string;
}>();

const nuxtApp = useNuxtApp();
const fileValue = (props.record as any)[props.filename];
const baseUrl = nuxtApp.$pb.files.getURL(props.record, fileValue, {
  thumb: props.thumb || `${props.width ?? 0}x${props.height ?? 0}`,
});
const src = `${baseUrl}?format=webp`;
const hasImage = !!fileValue;
</script>
<template>
  <img v-if="hasImage" :src="src" :width="width" :height="height" />
  <div v-else class="flex items-center justify-center bg-gray-200"
    :style="{ width: width ? `${width}px` : '100%', height: height ? `${height}px` : '200px' }">
    <span class="text-gray-500">Image non disponible</span>
  </div>
</template>
