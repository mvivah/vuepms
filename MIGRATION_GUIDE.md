# PMS to VuePMS Migration Guide

This document outlines the migration of the Laravel PMS (Pharmacy Management System) application from Blade templates to Vue.js with Inertia.js.

## ✅ Completed Steps

### 1. Backend Setup

#### Dependencies Added to `composer.json`
- `barryvdh/laravel-dompdf` - PDF generation
- `intervention/image` & `intervention/image-laravel` - Image processing
- `maatwebsite/excel` - Excel export functionality
- `phpoffice/phpspreadsheet` - Spreadsheet manipulation

#### Models Migrated
All models from `/pms/app/Models` have been copied to `/vuepms/app/Models`:
- Batch.php
- Brand.php
- Category.php
- Email.php
- ErrorLog.php
- Expense.php
- ExpenseType.php
- Inventory.php
- Invoice.php
- Login.php
- MailingList.php
- Product.php
- ProductPurchase.php
- ProductSale.php
- Purchase.php
- Receipt.php
- Role.php
- Sale.php
- Setting.php
- Supplier.php
- Unit.php
- User.php

#### Database Migrations
All migration files have been copied from `/pms/database/migrations` to `/vuepms/database/migrations`.

#### Helper Files
- `app/Helpers/General.php` copied and configured in `composer.json` autoload

### 2. Controllers Created (Inertia-enabled)

The following controllers have been converted to use Inertia responses:

✅ **BrandController.php** - Complete CRUD for brands
✅ **CategoryController.php** - Complete CRUD for categories  
✅ **SupplierController.php** - Complete CRUD for suppliers with status toggling
✅ **UnitController.php** - Complete CRUD for measurement units
✅ **ProductController.php** - Complete with:
  - CRUD operations
  - Image upload handling
  - EOQ (Economic Order Quantity) reports
  - Expired products reports
  - Product selection for sales

### 3. Routes Configuration

Routes have been set up in `/vuepms/routes/web.php` for:
- Categories (index, create, store, edit, update, destroy, list)
- Brands (index, create, store, edit, update, destroy, list)
- Suppliers (index, create, store, edit, update, destroy, list, toggle-status)
- Units (index, create, store, edit, update, destroy, list)
- Products (full CRUD + reports)

## 🚧 Next Steps Required

### 1. Create Remaining Controllers

You'll need to create Inertia versions of these controllers:

#### High Priority:
- **RoleController.php** - User role management
- **UserController.php** - User management with avatar upload
- **PurchaseController.php** - Purchase orders and items
- **SaleController.php** - Sales transactions
- **InventoryController.php** - Inventory tracking
- **InvoiceController.php** - Invoice management
- **ReceiptController.php** - Receipt management

#### Medium Priority:
- **ExpenseController.php** - Expense tracking
- **ExpenseTypeController.php** - Expense categories
- **SettingController.php** - Application settings
- **MailingListController.php** - Email marketing lists

#### Supporting:
- **HomeController.php** - Dashboard
- **TurnoverController.php** - Financial reports
- **DataTableController.php** - Data table utilities (may not be needed with Vue components)

### 2. Create Vue Pages

Create Vue components in `/vuepms/resources/js/pages/` for each module:

```
pages/
├── Brands/
│   ├── Index.vue
│   └── Create.vue (handles both create and edit)
├── Categories/
│   ├── Index.vue
│   └── Create.vue
├── Products/
│   ├── Index.vue
│   ├── Create.vue
│   └── Show.vue
├── Suppliers/
│   ├── Index.vue
│   └── Create.vue
├── Units/
│   ├── Index.vue
│   └── Create.vue
├── Purchases/
│   ├── Index.vue
│   ├── Create.vue
│   └── Show.vue
├── Sales/
│   ├── Index.vue
│   ├── Create.vue
│   └── Show.vue
├── Inventories/
│   ├── Index.vue
│   └── Report.vue
├── Expenses/
│   ├── Index.vue
│   └── Create.vue
├── Users/
│   ├── Index.vue
│   ├── Create.vue
│   └── Show.vue
├── Roles/
│   ├── Index.vue
│   └── Create.vue
└── Reports/
    └── Products/
        ├── EOQ.vue
        ├── Expired.vue
        └── Results.vue
```

### 3. Create Shared Components

Build reusable components in `/vuepms/resources/js/components/`:

#### Data Display:
- **DataTable.vue** - Reusable table with sorting, filtering, pagination
- **Card.vue** - Container component
- **Badge.vue** - Status indicators
- **EmptyState.vue** - No data placeholder

#### Forms:
- **FormInput.vue** - Text input with validation
- **FormSelect.vue** - Dropdown select
- **FormTextarea.vue** - Multi-line text input
- **FormFileUpload.vue** - File upload with preview
- **FormDatePicker.vue** - Date selection
- **FormCheckbox.vue** - Checkbox input
- **FormRadio.vue** - Radio button

#### UI Elements:
- **Button.vue** - Styled buttons
- **Modal.vue** - Modal dialogs
- **Alert.vue** - Success/error messages
- **ConfirmDialog.vue** - Confirmation prompts
- **LoadingSpinner.vue** - Loading indicator
- **Pagination.vue** - Page navigation

### 4. Install and Run Dependencies

```bash
cd /var/www/html/vuepms

# Install PHP dependencies
composer install

# Install Node dependencies
npm install

# Run migrations
php artisan migrate

# Build frontend assets
npm run dev
```

### 5. Configuration Files to Review

Check and copy necessary configuration from `/pms/config/` if needed:
- `config/dompdf.php` (if exists)
- `config/excel.php` (if exists)
- Any custom configuration files

### 6. Public Assets

Copy any required assets:
```bash
# Copy product images
cp -r /var/www/html/pms/public/storage/products /var/www/html/vuepms/public/storage/

# Copy other public assets as needed
cp -r /var/www/html/pms/public/images /var/www/html/vuepms/public/
cp -r /var/www/html/pms/public/css /var/www/html/vuepms/public/
```

### 7. Environment Configuration

Update `/vuepms/.env`:
```env
APP_NAME="VuePMS"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost

# Database configuration
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password

# ... other configurations
```

## 🎯 Key Differences from Original PMS

### Backend Changes:
1. **Inertia Responses** - Controllers return `Inertia::render()` instead of `view()`
2. **Flash Messages** - Using `back()->with('success', 'message')` instead of JSON responses
3. **Gate Checks** - Authorization remains the same but redirects to Inertia pages

### Frontend Architecture:
1. **Vue 3 Composition API** - Modern reactive framework
2. **Inertia.js** - SPA-like experience without API endpoints
3. **Vite** - Fast build tooling (replacing webpack.mix.js)
4. **TypeScript** - Type safety (optional but configured)
5. **Tailwind CSS v4** - Utility-first CSS framework

### Data Loading:
- **Before (Blade)**: Laratables for server-side data tables
- **After (Inertia)**: Client-side data tables with Vue components or server-side with Inertia pagination

## 📋 Development Workflow

1. **Start development server**:
   ```bash
   composer dev
   # OR manually:
   php artisan serve & npm run dev
   ```

2. **Create a new feature**:
   - Add route in `routes/web.php`
   - Create/update controller method
   - Create Vue page component
   - Test functionality

3. **Build for production**:
   ```bash
   npm run build
   ```

## 🔍 Testing

Run tests:
```bash
php artisan test
```

## 📚 Resources

- [Inertia.js Documentation](https://inertiajs.com/)
- [Vue 3 Documentation](https://vuejs.org/)
- [Laravel 12 Documentation](https://laravel.com/docs)
- [Tailwind CSS v4](https://tailwindcss.com/)

## 🤝 Contributing

When adding new features:
1. Follow the established pattern for controllers (Inertia responses)
2. Create reusable Vue components
3. Use TypeScript interfaces for props
4. Add proper validation on both client and server
5. Test thoroughly before committing

---

**Status**: Backend structure complete, frontend components need to be built
**Last Updated**: November 17, 2025
