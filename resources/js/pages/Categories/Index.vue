<script setup lang="ts">
import { ref } from 'vue'
import { Head, Link, router } from '@inertiajs/vue3'

interface Category {
  id: number
  name: string
  description: string | null
  created_at: string
  updated_at: string
}

interface Props {
  categories: Category[]
}

const props = defineProps<Props>()

const deleteCategory = (id: number) => {
  if (confirm('Are you sure you want to delete this category?')) {
    router.delete(`/categories/delete/${id}`, {
      onSuccess: () => {
        // Category deleted successfully
      },
    })
  }
}
</script>

<template>
  <Head title="Categories" />

  <div class="py-12">
    <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
      <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
        <div class="p-6">
          <div class="mb-6 flex items-center justify-between">
            <h2 class="text-2xl font-semibold text-gray-800">Categories</h2>
            <Link
              href="/categories/create"
              class="rounded-md bg-blue-600 px-4 py-2 text-white hover:bg-blue-700"
            >
              Add New Category
            </Link>
          </div>

          <!-- Categories Table -->
          <div class="overflow-x-auto">
            <table class="min-w-full divide-y divide-gray-200">
              <thead class="bg-gray-50">
                <tr>
                  <th
                    class="px-6 py-3 text-left text-xs font-medium uppercase tracking-wider text-gray-500"
                  >
                    Name
                  </th>
                  <th
                    class="px-6 py-3 text-left text-xs font-medium uppercase tracking-wider text-gray-500"
                  >
                    Description
                  </th>
                  <th
                    class="px-6 py-3 text-left text-xs font-medium uppercase tracking-wider text-gray-500"
                  >
                    Created At
                  </th>
                  <th
                    class="px-6 py-3 text-right text-xs font-medium uppercase tracking-wider text-gray-500"
                  >
                    Actions
                  </th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200 bg-white">
                <tr v-for="category in categories" :key="category.id">
                  <td class="whitespace-nowrap px-6 py-4 text-sm font-medium text-gray-900">
                    {{ category.name }}
                  </td>
                  <td class="px-6 py-4 text-sm text-gray-500">
                    {{ category.description || '-' }}
                  </td>
                  <td class="whitespace-nowrap px-6 py-4 text-sm text-gray-500">
                    {{ new Date(category.created_at).toLocaleDateString() }}
                  </td>
                  <td class="whitespace-nowrap px-6 py-4 text-right text-sm font-medium">
                    <Link
                      :href="`/categories/edit/${category.id}`"
                      class="mr-3 text-blue-600 hover:text-blue-900"
                    >
                      Edit
                    </Link>
                    <button
                      @click="deleteCategory(category.id)"
                      class="text-red-600 hover:text-red-900"
                    >
                      Delete
                    </button>
                  </td>
                </tr>
                <tr v-if="categories.length === 0">
                  <td colspan="4" class="px-6 py-4 text-center text-sm text-gray-500">
                    No categories found. Create your first category!
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
