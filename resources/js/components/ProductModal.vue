<template>
  <div
    class="fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50 p-4"
    @click.self="emit('close')"
  >
    <div class="relative w-full max-w-md rounded-xl bg-white shadow-lg dark:bg-gray-800">
      <!-- Header -->
      <div
        class="flex items-center justify-between border-b border-gray-200 p-6 dark:border-gray-700"
      >
        <h2 class="text-xl font-bold text-gray-900 dark:text-white">
          {{ isEditing ? 'Editar Produto' : 'Novo Produto' }}
        </h2>
        <button
          @click="emit('close')"
          class="text-gray-400 hover:text-gray-600 dark:hover:text-gray-300"
        >
          <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        </button>
      </div>

      <!-- Form -->
      <form @submit.prevent="handleSubmit" class="space-y-4 p-6">
        <!-- Name -->
        <div>
          <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
            Nome <span class="text-red-500">*</span>
          </label>
          <input
            v-model="form.name"
            type="text"
            required
            placeholder="Nome do produto"
            class="w-full rounded-lg border border-gray-300 bg-white px-4 py-2 text-gray-900 placeholder-gray-500 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:placeholder-gray-400"
          />
          <span v-if="errors.name" class="text-sm text-red-500">{{ errors.name }}</span>
        </div>

        <!-- Description -->
        <div>
          <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
            Descrição <span class="text-red-500">*</span>
          </label>
          <textarea
            v-model="form.description"
            required
            rows="3"
            placeholder="Descrição do produto"
            class="w-full rounded-lg border border-gray-300 bg-white px-4 py-2 text-gray-900 placeholder-gray-500 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:placeholder-gray-400"
          />
          <span v-if="errors.description" class="text-sm text-red-500">{{
            errors.description
          }}</span>
        </div>

        <!-- Price -->
        <div>
          <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
            Preço (R$) <span class="text-red-500">*</span>
          </label>
          <input
            v-model="form.price"
            type="number"
            step="0.01"
            min="0"
            required
            placeholder="0.00"
            class="w-full rounded-lg border border-gray-300 bg-white px-4 py-2 text-gray-900 placeholder-gray-500 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:placeholder-gray-400"
          />
          <span v-if="errors.price" class="text-sm text-red-500">{{ errors.price }}</span>
        </div>

        <!-- Image -->
        <div>
          <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
            Imagem
          </label>
          <input
            ref="imageInput"
            type="file"
            accept="image/*"
            @change="handleImageChange"
            class="w-full rounded-lg border border-gray-300 bg-white px-4 py-2 text-gray-900 file:border-0 file:bg-gray-100 file:px-4 file:py-2 file:text-sm file:font-medium file:text-gray-700 hover:file:bg-gray-200 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:file:bg-gray-600 dark:file:text-gray-300 dark:hover:file:bg-gray-500"
          />
          <span v-if="errors.image" class="text-sm text-red-500">{{ errors.image }}</span>

          <!-- Image Preview -->
          <div v-if="imagePreview || (isEditing && product?.image)" class="mt-3">
            <p class="text-sm text-gray-600 dark:text-gray-400 mb-2">Preview:</p>
            <img
              :src="imagePreview || `/storage/${product?.image}`"
              :alt="form.name"
              class="max-h-32 max-w-xs rounded-lg object-cover"
            />
          </div>
        </div>

        <!-- Buttons -->
        <div class="flex gap-3 border-t border-gray-200 pt-4 dark:border-gray-700">
          <button
            type="button"
            @click="emit('close')"
            class="flex-1 rounded-lg bg-gray-100 px-4 py-2 font-medium text-gray-700 hover:bg-gray-200 transition-colors dark:bg-gray-700 dark:text-gray-300 dark:hover:bg-gray-600"
          >
            Cancelar
          </button>
          <button
            type="submit"
            :disabled="isSubmitting"
            class="flex-1 rounded-lg bg-blue-600 px-4 py-2 font-medium text-white hover:bg-blue-700 transition-colors disabled:opacity-50 dark:bg-blue-700 dark:hover:bg-blue-800"
          >
            {{ isSubmitting ? 'Salvando...' : isEditing ? 'Atualizar' : 'Criar' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, watch } from 'vue'

interface Product {
  id?: number
  name: string
  description: string
  price: number
  image?: string | null
}

interface FormData {
  name: string
  description: string
  price: number | string
  image?: File | null
}

const emit = defineEmits<{
  close: []
  save: [formData: FormData]
}>()

const props = defineProps<{
  product?: Product | null
  isEditing: boolean
}>()

const imageInput = ref<HTMLInputElement>()
const isSubmitting = ref(false)
const imagePreview = ref<string | null>(null)

const form = reactive<FormData>({
  name: props.product?.name || '',
  description: props.product?.description || '',
  price: props.product?.price || '',
  image: null,
})

const errors = reactive<Partial<Record<keyof FormData, string>>>({})

const handleImageChange = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]

  if (file) {
    form.image = file
    const reader = new FileReader()
    reader.onload = (e) => {
      imagePreview.value = e.target?.result as string
    }
    reader.readAsDataURL(file)
  }
}

const handleSubmit = async () => {
  isSubmitting.value = true
  const formData = new FormData()

  formData.append('name', form.name)
  formData.append('description', form.description)
  formData.append('price', form.price.toString())

  if (form.image) {
    formData.append('image', form.image)
  }

  emit('save', formData)
  isSubmitting.value = false
}

watch(
  () => props.product,
  (newProduct) => {
    if (newProduct) {
      form.name = newProduct.name
      form.description = newProduct.description
      form.price = newProduct.price
      imagePreview.value = null
      form.image = null
    }
  }
)
</script>
