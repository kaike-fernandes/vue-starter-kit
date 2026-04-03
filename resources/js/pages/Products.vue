<template>
  <Head title="Produtos" />

  <div class="flex h-full flex-1 flex-col gap-4 overflow-x-auto rounded-xl p-4">
    <!-- Header -->
    <div class="flex items-center justify-between">
      <h1 class="text-3xl font-bold text-gray-900 dark:text-white">Produtos</h1>
      <button
        @click="showCreateModal = true"
        class="inline-flex items-center justify-center rounded-lg bg-blue-600 px-4 py-2 font-medium text-white hover:bg-blue-700 transition-colors dark:bg-blue-700 dark:hover:bg-blue-800"
      >
        <span class="mr-2">+</span>
        Novo Produto
      </button>
    </div>

    <!-- Loading State -->
    <div v-if="loading" class="flex items-center justify-center py-12">
      <div class="text-center">
        <div class="mb-4 inline-flex h-12 w-12 animate-spin rounded-full border-4 border-gray-300 border-t-blue-600 dark:border-gray-600 dark:border-t-blue-500"></div>
        <p class="text-gray-500 dark:text-gray-400">Carregando produtos...</p>
      </div>
    </div>

    <!-- Products Grid -->
    <div
      v-else-if="products.length > 0"
      class="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-3"
    >
      <div
        v-for="product in products"
        :key="product.id"
        class="overflow-hidden rounded-xl border border-gray-200 bg-white shadow-sm transition-shadow hover:shadow-md dark:border-gray-700 dark:bg-gray-800"
      >
        <!-- Product Image -->
        <div class="relative h-48 w-full overflow-hidden bg-gray-100 dark:bg-gray-700">
          <img
            v-if="product.image"
            :src="`/storage/${product.image}`"
            :alt="product.name"
            class="h-full w-full object-cover"
          />
          <div v-else class="flex h-full w-full items-center justify-center">
            <svg
              class="h-12 w-12 text-gray-400 dark:text-gray-500"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"
              />
            </svg>
          </div>
        </div>

        <!-- Product Info -->
        <div class="flex flex-col p-4">
          <h3 class="mb-2 text-lg font-semibold text-gray-900 dark:text-white">
            {{ product.name }}
          </h3>
          <p class="mb-3 line-clamp-2 flex-1 text-sm text-gray-600 dark:text-gray-400">
            {{ product.description }}
          </p>

          <!-- Price and Author -->
          <div class="mb-4 flex items-center justify-between border-t border-gray-200 pt-3 dark:border-gray-700">
            <span class="text-2xl font-bold text-blue-600 dark:text-blue-400">
              R$ {{ formatPrice(product.price) }}
            </span>
            <span class="text-xs text-gray-500 dark:text-gray-400">
              por {{ product.user.name }}
            </span>
          </div>

          <!-- Actions -->
          <div v-if="isOwner(product)" class="flex gap-2">
            <button
              @click="editProduct(product)"
              class="flex-1 rounded-lg bg-amber-500 px-3 py-2 text-sm font-medium text-white hover:bg-amber-600 transition-colors dark:bg-amber-600 dark:hover:bg-amber-700"
            >
              Editar
            </button>
            <button
              @click="deleteProduct(product)"
              class="flex-1 rounded-lg bg-red-500 px-3 py-2 text-sm font-medium text-white hover:bg-red-600 transition-colors dark:bg-red-600 dark:hover:bg-red-700"
            >
              Deletar
            </button>
          </div>
          <div v-else class="text-xs text-gray-400 italic">
            Você não é o proprietário
          </div>
        </div>
      </div>
    </div>

    <!-- Empty State -->
    <div
      v-else
      class="flex flex-col items-center justify-center rounded-xl border-2 border-dashed border-gray-300 py-12 dark:border-gray-600"
    >
      <svg
        class="mb-4 h-12 w-12 text-gray-400 dark:text-gray-500"
        fill="none"
        stroke="currentColor"
        viewBox="0 0 24 24"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4"
        />
      </svg>
      <p class="mb-4 text-gray-600 dark:text-gray-400">
        Nenhum produto criado ainda
      </p>
      <button
        @click="showCreateModal = true"
        class="inline-flex items-center justify-center rounded-lg bg-blue-600 px-4 py-2 font-medium text-white hover:bg-blue-700 transition-colors dark:bg-blue-700 dark:hover:bg-blue-800"
      >
        <span class="mr-2">+</span>
        Criar o Primeiro Produto
      </button>
    </div>
  </div>

  <!-- Create/Edit Modal -->
  <ProductModal
    v-if="showCreateModal || showEditModal"
    :product="editingProduct"
    :isEditing="showEditModal"
    @close="closeModals"
    @save="saveProduct"
  />
</template>

<script setup lang="ts">
import { Head } from '@inertiajs/vue3';
import { ref, onMounted, computed } from 'vue';
import { usePage } from '@inertiajs/vue3';
import ProductModal from '@/components/ProductModal.vue';

interface User {
  id: number;
  name: string;
  email: string;
}

interface Product {
  id: number;
  name: string;
  description: string;
  price: number;
  image: string | null;
  user_id: number;
  user: User;
  created_at: string;
  updated_at: string;
}

defineOptions({
  layout: {
    breadcrumbs: [
      {
        title: 'Produtos',
        href: '/products',
      },
    ],
  },
});

const page = usePage();
const currentUserId = computed(() => (page.props.auth as any).user?.id);

const products = ref<Product[]>([]);
const loading = ref(true);
const showCreateModal = ref(false);
const showEditModal = ref(false);
const editingProduct = ref<Product | null>(null);

const isOwner = (product: Product) => {
  return currentUserId.value === product.user_id;
};

const formatPrice = (price: number) => {
  return parseFloat(price.toString()).toFixed(2).replace('.', ',');
};

const fetchProducts = async () => {
  try {
    loading.value = true;
    const response = await fetch('/api/products');
    products.value = await response.json();
  } catch (error) {
    console.error('Erro ao carregar produtos:', error);
  } finally {
    loading.value = false;
  }
};

const editProduct = (product: Product) => {
  editingProduct.value = { ...product };
  showEditModal.value = true;
};

const closeModals = () => {
  showCreateModal.value = false;
  showEditModal.value = false;
  editingProduct.value = null;
};

const saveProduct = async (formData: FormData) => {
  try {
    const isEditing = showEditModal.value;
    const url = isEditing ? `/api/products/${editingProduct.value?.id}` : '/api/products';

    if (isEditing) {
      formData.append('_method', 'PUT');
    }

    const response = await fetch(url, {
      method: 'POST',
      body: formData,
      headers: {
        'X-Requested-With': 'XMLHttpRequest',
      },
    });

    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.message || 'Erro ao salvar produto');
    }

    closeModals();
    await fetchProducts();
  } catch (error) {
    alert(
      'Erro ao salvar produto: ' +
        (error instanceof Error ? error.message : 'Erro desconhecido')
    );
  }
};

const deleteProduct = async (product: Product) => {
  if (!confirm(`Tem certeza que deseja deletar "${product.name}"?`)) {
    return;
  }

  try {
    const response = await fetch(`/api/products/${product.id}`, {
      method: 'DELETE',
      headers: {
        'X-Requested-With': 'XMLHttpRequest',
      },
    });

    if (!response.ok) {
      throw new Error('Erro ao deletar produto');
    }

    await fetchProducts();
  } catch (error) {
    alert(
      'Erro ao deletar produto: ' +
        (error instanceof Error ? error.message : 'Erro desconhecido')
    );
  }
};

onMounted(() => {
  fetchProducts();
});
</script>
