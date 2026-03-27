<script setup lang="ts">
// throw new Error('Auxilio, esto no se puede hacer.');
// throw createError({
//   statusCode: 500,
//   statusMessage: 'No se puede dividir entre 0',
//   data: {
//     myCustomField: true,
//     myOtherField: 'Hola mundo',
//   },
// });
const { products, total, currentPage, perPage, status } = await usePaginatedProducts()
</script>

<template>
  <UPageCTA
    orientation="horizontal"
    title="Productos"
    description="Descubre nuestros productos y servicios."
  >
    <template #default>
      <div class="flex items-center justify-center">
        <UIcon name="i-lucide-box" :size="150" />
      </div>
    </template>
  </UPageCTA>

  <div class="mt-10" />

  <div v-if="status === 'pending'" class="flex items-center justify-center h-64">
    <UIcon name="i-lucide-loader-circle" class="size-8 animate-spin text-slate-400" />
  </div>
  <template v-else>
    <ProductsGrid :products="products" />
    <SharedPagination :total="total" :current-page="currentPage" :per-page="perPage" />
  </template>

</template>

<style scoped></style>
