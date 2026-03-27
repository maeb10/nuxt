<template>
  <div class="space-y-6">
    <!-- Header with Action Button -->
    <div class="flex items-center justify-between">
      <div>
        <h1 class="text-3xl font-bold text-gray-900 dark:text-white">
          Productos
        </h1>
        <p class="text-gray-600 dark:text-gray-400 mt-2">
          Gestiona y organiza tu catálogo de productos
        </p>
      </div>
      <UButton
        icon="i-lucide-plus"
        label="Agregar Producto"
        color="primary"
        size="lg"
      />
    </div>

    <UTable :data="products" :columns="columns" class="flex-1" />

    <!-- Card de detalle -->
    <div v-if="selectedProduct" class="border rounded-xl p-6 bg-white dark:bg-gray-900 shadow space-y-4">
      <div class="flex items-center justify-between">
        <h2 class="text-xl font-semibold text-gray-900 dark:text-white">Detalle del Producto</h2>
        <UButton icon="i-lucide-x" variant="ghost" color="neutral" @click="selectedProduct = null" />
      </div>

      <div class="flex gap-6">
        <img
          v-if="selectedProduct.images?.[0]"
          :src="selectedProduct.images[0]"
          alt="Imagen del producto"
          class="w-24 h-24 object-cover rounded-lg"
        />
        <div class="space-y-2 flex-1">
          <div><span class="font-medium text-gray-500">ID:</span> #{{ selectedProduct.id }}</div>
          <div><span class="font-medium text-gray-500">Nombre:</span> {{ selectedProduct.name }}</div>
          <div><span class="font-medium text-gray-500">Descripción:</span> {{ selectedProduct.description }}</div>
          <div><span class="font-medium text-gray-500">Precio:</span> {{ formatCurrency(selectedProduct.price) }}</div>
          <div><span class="font-medium text-gray-500">Creado:</span> {{ dayMonthYearFormat(new Date(selectedProduct.createdAt)) }}</div>
          <div class="flex gap-1 flex-wrap">
            <span class="font-medium text-gray-500 mr-1">Tags:</span>
            <UBadge
              v-for="tag in selectedProduct.tags"
              :key="tag"
              size="md"
              color="primary"
              variant="subtle"
            >
              {{ tag }}
            </UBadge>
          </div>
        </div>
      </div>
    </div>

    <SharedPagination :total="total" :current-page="currentPage" :per-page="perPage"/>
  </div>
</template>

<script setup lang="ts">
import { h, resolveComponent } from 'vue';
import type { TableColumn } from '@nuxt/ui';
const UBadge = resolveComponent('UBadge');
const UCheckbox = resolveComponent('UCheckbox');


const { products, total, currentPage, perPage, status } = await usePaginatedProducts()

const selectedProduct = ref<Product | null>(null)

function toggleSelect(product: Product) {
  // Si ya está seleccionado, lo deselecciona; si no, reemplaza la selección anterior
  selectedProduct.value = selectedProduct.value?.id === product.id ? null : product
}

const columns: TableColumn<Product>[] = [
  {
    id: 'select',
    header: '',
    cell: ({ row }) => {
      const product = row.original
      return h(UCheckbox, {
        modelValue: selectedProduct.value?.id === product.id,
        'onUpdate:modelValue': () => toggleSelect(product),
      })
    },
  },
  {
    accessorKey: 'id',
    header: '#',
    cell: ({ row }) => `#${row.getValue('id')}`,
  },
  {
    accessorKey: 'images',
    header: 'Imagen',
    cell: ({ row }) => {
      const images = row.getValue('images') as string[]
      const url = Array.isArray(images) && images.length > 0 ? images[0] : ''

      if (!url) {
        return h('span', { class: 'text-gray-500' }, 'Sin imagen')
      }

      return h('img', {
        src: url,
        alt: 'Imagen del producto',
        style: 'width: 48px; height: 48px; object-fit: cover; border-radius: 0.5rem'
      })
    }
  },
  {
    accessorKey: 'name',
    header: 'Nombre',
  },
  {
    accessorKey: 'description',
    header: 'Descripción',
    cell: ({ row }) => {
      return h(
        'div',
        {
          style:
            'white-space: normal; word-break: break-word; max-width: 300px;',
          class: 'truncate-text',
        },
        String(row.getValue('description')).slice(0, 50) + '...'
      );
    },
  },
  {
    accessorKey: 'price',
    header: 'Precio',
    cell: ({ row }) => formatCurrency(row.getValue('price')),
  },
  {
    accessorKey: 'tags',
    header: 'Tags',
    cell: ({ row }) => {
      const tags = row.getValue('tags') as string[]
      if (!Array.isArray(tags)) return ''
      return h('div', { class: 'flex gap-1 flex-wrap' },
        tags.map(tag =>
          h(
            UBadge,
            {
              size: 'md',
              color: 'primary',
              variant: 'subtle',
              class: 'mr-0.5',
            },
            () => tag
          )
        )
      )
    }
  },
  {
    accessorKey: 'createdAt',
    header: 'Creado',
    cell: ({ row }) => {
      const value = row.getValue('createdAt');
      return value ? dayMonthYearFormat(new Date(value as string)) : ''
    },
  },
];
</script>
