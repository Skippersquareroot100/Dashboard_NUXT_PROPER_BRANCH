
<template>
  <div class="flex h-screen bg-gray-50">
    <!-- Sidebar -->
    <aside class="w-2/5 sm:w-64 bg-gray-900 text-white transition-all duration-300 flex-shrink-0 overflow-auto"> 
      <div class="p-2 sm:p-4">
        <div class="flex items-center gap-2 text-sm sm:text-xl font-bold">
          <div class="w-6 sm:w-8 h-6 sm:h-8 bg-red-500 rounded-lg"></div>
          <span class="ml-3">{{ shop.name ? shop.name + "'s" : 'ACTIVE' }}<span class="text-red-500">Platform</span></span>
        </div>
      </div>

      <!-- Search Bar -->
      <div class="px-2 sm:px-4 mt-2 sm:mt-6">
        <input v-model="searchQuery" type="search" placeholder="Search"
          class="w-full px-2 sm:px-4 py-1 sm:py-2 bg-gray-800 rounded-md text-xs sm:text-sm" />
      </div>

      <!-- Menu Items -->
      <nav class="mt-3 sm:mt-6">
        <template v-for="(item, index) in filteredMenuItems" :key="index">
          <router-link :to="item.path" class="flex items-center px-2 sm:px-4 py-2 sm:py-3 text-gray-300 hover:bg-gray-800">
            <component :is="item.icon" class="w-5 h-5" />
            <span class="ml-3">{{ item.name }}</span>
          </router-link>
        </template>
      </nav>
      <router-link to="/" class=" mx-7 py-80  text-red-500    text-left block">
      Logout
    </router-link>
    </aside>

    <!-- Main Content -->
    <div class="flex-1 overflow-auto transition-all duration-300">
      <!-- Header (Sticky at Top, Outside of Main) -->
      <header class="bg-white border-b sticky top-0 z-10">
        <div class="flex items-center justify-between px-6 py-4">
          <div class="flex items-center space-x-4">
            <!-- Add content if necessary -->
          </div>
          <div class="flex items-center space-x-4">
    <router-link to="/manageShop" class="w-9 h-9 rounded-full overflow-hidden cursor-pointer">
      <img 
        v-if="shop.logo" 
        :src="shop.logo" 
        alt="Shop Logo" 
        class="w-full h-full object-cover" />
      <img 
        v-else 
        src="/avatar-placeholder.png" 
        alt="Default Logo" 
        class="w-full h-full object-cover" />
    </router-link>
    </div>
        </div>
      </header>
       
      <!-- Dashboard Content -->
      <main class="p-4 mt-0.5">
          
        <div class="w-full max-w-4xl mx-auto p-4">
    <!-- Invoice Header -->
    <div class="flex justify-between items-center mb-6">
      <div>
        <h1 class="text-2xl font-bold">Invoice</h1>
      </div>
      <button
        @click="downloadInvoice"
        class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700"
      >
        Download PDF
      </button>
    </div>

    <!-- Customer Details -->
    <div class="mb-6 bg-white shadow-md p-4 rounded-lg">
      <h3 class="font-semibold mb-2">Customer Information</h3>
      <div class="grid grid-cols-1 sm:grid-cols-3 gap-2">
        <input
          v-model="customer.name"
          type="text"
          placeholder="Customer Name"
          class="border border-gray-300 p-2 rounded-md w-full"
        />
        <input
          v-model="customer.email"
          type="email"
          placeholder="Customer Email"
          class="border border-gray-300 p-2 rounded-md w-full"
        />
        <input
          v-model="customer.phone"
          type="text"
          placeholder="Customer Phone"
          class="border border-gray-300 p-2 rounded-md w-full"
        />
      </div>
    </div>

    <!-- Invoice Items Table -->
    <div class="mb-6 bg-white shadow-md p-4 rounded-lg">
  <h3 class="font-semibold mb-2">Invoice Items</h3>

  <!-- Responsive Table Wrapper -->
  <div class="overflow-x-auto">
    <table class="w-full border-collapse border border-gray-300">
      <thead>
        <tr class="bg-gray-200">
          <th class="px-4 py-2 border text-left">Item</th>
          <th class="px-4 py-2 border text-left">Description</th>
          <th class="px-4 py-2 border text-right">Unit Price</th>
          <th class="px-4 py-2 border text-right">Quantity</th>
          <th class="px-4 py-2 border text-right">Total</th>
          <th class="px-4 py-2 border text-center">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in invoice.items" :key="index">
          <td class="px-4 py-2 border">
            <input
              v-model="item.name"
              type="text"
              placeholder="Item Name"
              class="border border-gray-300 p-2 rounded-md w-full"
            />
          </td>
          <td class="px-4 py-2 border">
            <input
              v-model="item.description"
              type="text"
              placeholder="Item Description"
              class="border border-gray-300 p-2 rounded-md w-full"
            />
          </td>
          <td class="px-4 py-2 border text-right">
            <input
              v-model.number="item.unitPrice"
              type="number"
              placeholder="Unit Price"
              class="border border-gray-300 p-2 rounded-md w-full text-right"
            />
          </td>
          <td class="px-4 py-2 border text-right">
            <input
              v-model.number="item.quantity"
              type="number"
              placeholder="Quantity"
              class="border border-gray-300 p-2 rounded-md w-full text-right"
            />
          </td>
          <td class="px-4 py-2 border text-right">
            {{ calculateItemTotal(item) }}
          </td>
          <td class="px-4 py-2 border text-center">
            <button
              @click="removeItem(index)"
              class="px-3 py-1 bg-red-600 text-white rounded-md hover:bg-red-700"
            >
              ✖
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <button
    @click="addItem"
    class="mt-4 px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700"
  >
    Add Item
  </button>
</div>


    <!-- Discount and Summary -->
    <div class="mb-6 bg-white shadow-md p-4 rounded-lg">
      <h3 class="font-semibold mb-2">Invoice Summary</h3>
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-2">
        <p class="font-bold">Discount</p>
        <input
          v-model.number="discount"
          type="number"
          placeholder="Discount (%)"
          class="border border-gray-300 p-2 rounded-md w-full"
        />
        <p class="font-bold">Tax</p>
        <input
          v-model.number="taxRate"
          type="number"
          placeholder="Tax (%)"
          class="border border-gray-300 p-2 rounded-md w-full"
        />
      </div>
      <div class="mt-4 text-right">
        <p><strong>Subtotal:</strong> ${{ subtotal }}</p>
        <p><strong>Discount:</strong> -${{ discountAmount }}</p>
        <p><strong>Tax ({{ taxRate }}%):</strong> ${{ tax }}</p>
        <p class="text-xl font-bold"><strong>Total:</strong> ${{ total }}</p>
      </div>
    </div>
  </div>


      </main>
    </div>
  </div>
</template>



<script setup>

const menuItems = [
  { name: "Dashboard", path: "/dashboard", icon: "LayoutDashboard", active: true },
  { name: "Products", path: "/products", icon: "Package" },
  { name: "Orders", path: "/orders", icon: "ShoppingCart" },
  { name: "Customers", path: "/customers", icon: "Package" },
  { name: "Reports", path: "/reports", icon: "BarChart" },
  { name: "Manage Shop", path: "/manageShop", icon: "BarChart" },
  { name: "Cupon", path: "/cupon", icon: "BarChart" },
  { name: "Invoicing", path: "/invoicing", icon: "BarChart" },
  { name: "Lucky Spin", path: "/luckyspin", icon: "BarChart" },
  { name: "Billing", path: "/billing", icon: "BarChart" },
];


// Reactive search query
const searchQuery = ref("");
const filteredMenuItems = computed(() => {
  return menuItems.filter(item => item.name.toLowerCase().includes(searchQuery.value.toLowerCase()));
});

const shop = ref({
  name: "", 
  logo: "",
  // Add other properties here as needed
});

onMounted(() => {
  const savedShop = localStorage.getItem("shopData");
  if (savedShop) {
    shop.value = JSON.parse(savedShop); // Populate shop data from localStorage
  }
});



import { ref, computed } from 'vue';

// Customer Information
const customer = ref({
  name: '',
  email: '',
  phone: '',
});

// Invoice Details
const invoice = ref({
  id: '12345',
  items: [{ name: '', description: '', unitPrice: 0, quantity: 0 }],
});

// Discount & Tax
const discount = ref(0);
const taxRate = ref(0); // User-defined tax percentage

// Add item method
const addItem = () => {
  invoice.value.items.push({ name: '', description: '', unitPrice: 0, quantity: 0 });
};

// Remove item method
const removeItem = (index) => {
  invoice.value.items.splice(index, 1);
};

// Calculate item total
const calculateItemTotal = (item) => {
  return (item.unitPrice * item.quantity).toFixed(2);
};

// Computed property for subtotal
const subtotal = computed(() => {
  return invoice.value.items.reduce((sum, item) => sum + (item.unitPrice * item.quantity), 0).toFixed(2);
});

// Computed property for discount amount
const discountAmount = computed(() => {
  return ((discount.value / 100) * subtotal.value).toFixed(2);
});

// Computed property for tax (User-defined)
const tax = computed(() => {
  return ((taxRate.value / 100) * (parseFloat(subtotal.value) - parseFloat(discountAmount.value))).toFixed(2);
});

// Computed property for total
const total = computed(() => {
  return (parseFloat(subtotal.value) - parseFloat(discountAmount.value) + parseFloat(tax.value)).toFixed(2);
});

// Download Invoice (Placeholder for PDF export functionality)
const downloadInvoice = () => {
  alert('Download PDF functionality is not implemented yet!');
};
</script>

















