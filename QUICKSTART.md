# Quick Start Guide - VuePMS

## Getting Started

### 1. Install Dependencies

```bash
cd /var/www/html/vuepms

# Install PHP dependencies
composer install

# Install Node.js dependencies
npm install
```

### 2. Environment Setup

Copy and configure your environment file:
```bash
cp .env.example .env
php artisan key:generate
```

Update `.env` with your database credentials:
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database_name
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

### 3. Database Setup

Run migrations:
```bash
php artisan migrate
```

(Optional) Seed the database:
```bash
php artisan db:seed
```

### 4. Storage Setup

Create storage link:
```bash
php artisan storage:link

# Create products directory
mkdir -p public/storage/products
chmod -R 775 storage bootstrap/cache
```

### 5. Start Development Server

```bash
# Option 1: Using composer script (recommended)
composer dev

# Option 2: Manually
php artisan serve &
npm run dev
```

Visit `http://localhost:8000`

## Creating Your First Module

Let's create the Brands module as an example:

### 1. The Controller Already Exists
`/app/Http/Controllers/BrandController.php` ✅

### 2. Create Vue Pages

Create `/resources/js/pages/Brands/Index.vue`:
```vue
<script setup lang="ts">
import { Head, Link, router } from '@inertiajs/vue3'

interface Brand {
  id: number
  name: string
  description: string
  created_at: string
}

defineProps<{ brands: Brand[] }>()

const deleteBrand = (id: number) => {
  if (confirm('Delete this brand?')) {
    router.delete(`/brands/delete/${id}`)
  }
}
</script>

<template>
  <Head title="Brands" />
  
  <div class="py-12">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <div class="mb-6 flex items-center justify-between">
        <h1 class="text-3xl font-bold">Brands</h1>
        <Link href="/brands/create" class="btn-primary">
          Add New Brand
        </Link>
      </div>

      <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
        <table class="min-w-full divide-y divide-gray-200">
          <thead class="bg-gray-50">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium uppercase text-gray-500">Name</th>
              <th class="px-6 py-3 text-left text-xs font-medium uppercase text-gray-500">Description</th>
              <th class="px-6 py-3 text-right text-xs font-medium uppercase text-gray-500">Actions</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 bg-white">
            <tr v-for="brand in brands" :key="brand.id">
              <td class="px-6 py-4">{{ brand.name }}</td>
              <td class="px-6 py-4">{{ brand.description }}</td>
              <td class="px-6 py-4 text-right">
                <Link :href="`/brands/edit/${brand.id}`" class="text-blue-600 hover:text-blue-900 mr-3">
                  Edit
                </Link>
                <button @click="deleteBrand(brand.id)" class="text-red-600 hover:text-red-900">
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
```

Create `/resources/js/pages/Brands/Create.vue`:
```vue
<script setup lang="ts">
import { useForm, Head, Link } from '@inertiajs/vue3'

interface Brand {
  id: number
  name: string
  description: string
}

const props = defineProps<{ brand?: Brand }>()

const form = useForm({
  name: props.brand?.name || '',
  description: props.brand?.description || '',
  brand_id: props.brand?.id || null,
})

const submit = () => {
  form.post('/brands/store')
}
</script>

<template>
  <Head :title="brand ? 'Edit Brand' : 'Create Brand'" />
  
  <div class="py-12">
    <div class="mx-auto max-w-2xl px-4 sm:px-6 lg:px-8">
      <div class="mb-6">
        <Link href="/brands" class="text-blue-600 hover:text-blue-900">
          ← Back to Brands
        </Link>
      </div>

      <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg p-6">
        <h2 class="mb-6 text-2xl font-bold">
          {{ brand ? 'Edit Brand' : 'Create Brand' }}
        </h2>

        <form @submit.prevent="submit" class="space-y-6">
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Name <span class="text-red-500">*</span>
            </label>
            <input
              v-model="form.name"
              type="text"
              required
              class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
            />
            <p v-if="form.errors.name" class="mt-1 text-sm text-red-600">
              {{ form.errors.name }}
            </p>
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700">
              Description <span class="text-red-500">*</span>
            </label>
            <textarea
              v-model="form.description"
              required
              rows="4"
              class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
            ></textarea>
            <p v-if="form.errors.description" class="mt-1 text-sm text-red-600">
              {{ form.errors.description }}
            </p>
          </div>

          <div class="flex justify-end space-x-3">
            <Link href="/brands" class="btn-secondary">Cancel</Link>
            <button type="submit" :disabled="form.processing" class="btn-primary">
              {{ form.processing ? 'Saving...' : (brand ? 'Update' : 'Create') }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
```

### 3. Test It

1. Visit `http://localhost:8000/brands`
2. Click "Add New Brand"
3. Fill the form and submit
4. View, edit, or delete brands

## Next Steps

Repeat this process for each module:
- ✅ Categories (example provided)
- ✅ Brands (example above)
- Units
- Suppliers  
- Products
- Users
- Roles
- Purchases
- Sales
- Expenses
- Invoices
- Receipts
- Inventory

## Useful Commands

```bash
# Clear cache
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# Generate IDE helper (optional)
php artisan ide-helper:generate
php artisan ide-helper:models

# Run tests
php artisan test

# Build for production
npm run build
```

## Troubleshooting

### "Class not found" errors
```bash
composer dump-autoload
```

### Frontend not updating
```bash
npm run dev
# or rebuild
npm run build
```

### Database errors
```bash
php artisan migrate:fresh
# or with seed
php artisan migrate:fresh --seed
```

## Project Structure

```
vuepms/
├── app/
│   ├── Http/Controllers/     # Inertia controllers
│   ├── Models/                # Eloquent models
│   └── Helpers/              # Helper functions
├── database/
│   ├── migrations/           # Database schema
│   └── seeders/              # Sample data
├── resources/
│   └── js/
│       ├── components/       # Reusable Vue components
│       ├── layouts/          # Page layouts
│       ├── pages/            # Page components (Inertia)
│       └── app.ts            # Main entry point
├── routes/
│   ├── web.php              # Web routes
│   └── auth.php             # Auth routes
└── public/
    └── storage/             # Public file storage
```

## Resources

- [Inertia.js Docs](https://inertiajs.com/)
- [Vue 3 Docs](https://vuejs.org/)
- [Laravel Docs](https://laravel.com/docs)
- [Tailwind CSS](https://tailwindcss.com/)

---

Happy coding! 🚀
