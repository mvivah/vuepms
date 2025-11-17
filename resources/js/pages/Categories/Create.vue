<script setup lang="ts">
import { useForm, Head, Link } from '@inertiajs/vue3'

interface Category {
  id: number
  name: string
  description: string | null
}

interface Props {
  category?: Category
}

const props = defineProps<Props>()

const form = useForm({
  name: props.category?.name || '',
  description: props.category?.description || '',
  category_id: props.category?.id || null,
})

const submit = () => {
  if (props.category) {
    form.put(`/categories/update/${props.category.id}`)
  } else {
    form.post('/categories/store')
  }
}
</script>

<template>
  <Head :title="category ? 'Edit Category' : 'Create Category'" />

  <div class="py-12">
    <div class="mx-auto max-w-2xl sm:px-6 lg:px-8">
      <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
        <div class="p-6">
          <div class="mb-6 flex items-center justify-between">
            <h2 class="text-2xl font-semibold text-gray-800">
              {{ category ? 'Edit Category' : 'Create Category' }}
            </h2>
            <Link href="/categories" class="text-blue-600 hover:text-blue-900">
              ← Back to List
            </Link>
          </div>

          <form @submit.prevent="submit" class="space-y-6">
            <!-- Name Field -->
            <div>
              <label for="name" class="block text-sm font-medium text-gray-700">
                Category Name <span class="text-red-500">*</span>
              </label>
              <input
                id="name"
                v-model="form.name"
                type="text"
                required
                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
                :class="{ 'border-red-500': form.errors.name }"
              />
              <p v-if="form.errors.name" class="mt-1 text-sm text-red-600">
                {{ form.errors.name }}
              </p>
            </div>

            <!-- Description Field -->
            <div>
              <label for="description" class="block text-sm font-medium text-gray-700">
                Description
              </label>
              <textarea
                id="description"
                v-model="form.description"
                rows="4"
                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
                :class="{ 'border-red-500': form.errors.description }"
              ></textarea>
              <p v-if="form.errors.description" class="mt-1 text-sm text-red-600">
                {{ form.errors.description }}
              </p>
            </div>

            <!-- Submit Button -->
            <div class="flex items-center justify-end space-x-3">
              <Link
                href="/categories"
                class="rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50"
              >
                Cancel
              </Link>
              <button
                type="submit"
                :disabled="form.processing"
                class="rounded-md bg-blue-600 px-4 py-2 text-sm font-medium text-white hover:bg-blue-700 disabled:opacity-50"
              >
                {{ form.processing ? 'Saving...' : category ? 'Update' : 'Create' }}
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
