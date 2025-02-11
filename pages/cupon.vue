
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
            
          
          <div class="flex flex-col h-screen bg-gray-50">
    <!-- Header -->
    <header class="bg-white shadow-md p-4">
      <div class="flex justify-between items-center">
        <h1 class="text-xl md:text-2xl font-bold">Coupons</h1>
        <button
          @click="exportCoupons"
          class="px-3 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 text-sm md:text-base"
        >
          Export
        </button>
      </div>
    </header>

    <!-- Filters Section -->
    <div class="flex flex-wrap gap-4 p-4 bg-white shadow-md">
      <!-- Select Status -->
      <div class="flex flex-col md:flex-row md:items-center space-y-2 md:space-y-0 md:space-x-2 w-full md:w-auto">
        <label for="status" class="text-sm">Select Status</label>
        <select
          id="status"
          v-model="filters.status"
          class="border border-gray-300 p-2 rounded-md text-sm w-full md:w-auto"
        >
          <option value="all">All</option>
          <option value="active">Active</option>
          <option value="expired">Expired</option>
        </select>
      </div>

      <!-- Search Coupon Code -->
      <div class="flex flex-col md:flex-row md:items-center space-y-2 md:space-y-0 md:space-x-2 w-full md:w-auto">
        <label for="searchCode" class="text-sm">Search Coupon Code</label>
        <input
          id="searchCode"
          v-model="filters.searchCode"
          type="text"
          placeholder="Enter coupon code"
          class="border border-gray-300 p-2 rounded-md text-sm w-full md:w-auto"
        />
      </div>
    </div>

    <!-- Add Coupon Form -->
    <div class="flex flex-col gap-4 p-4 bg-white shadow-md">
      <h3 class="text-lg md:text-xl font-semibold">Add New Coupon</h3>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-5 gap-2">
        <input
          v-model="newCoupon.code"
          type="text"
          placeholder="Coupon Code"
          class="border border-gray-300 p-2 text-sm rounded-md"
        />
        <input
          v-model="newCoupon.discount"
          type="number"
          placeholder="Discount (%)"
          class="border border-gray-300 p-2 text-sm rounded-md"
        />
        <input
          v-model="newCoupon.validUntil"
          type="date"
          class="border border-gray-300 p-2 text-sm rounded-md"
        />
        <select
          v-model="newCoupon.status"
          class="border border-gray-300 p-2 text-sm rounded-md"
        >
          <option value="active">Active</option>
          <option value="expired">Expired</option>
        </select>
        <button
          @click="addCoupon"
          class="px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700"
        >
          Add Coupon
        </button>
      </div>
    </div>

    <!-- Coupons Table -->
    <div class="p-4 overflow-auto">
      <div class="overflow-x-auto">
        <table class="min-w-full border-collapse border border-gray-300">
          <thead class="bg-gray-200">
            <tr>
              <th class="border-b p-2 text-sm md:text-base">Coupon ID</th>
              <th class="border-b p-2 text-sm md:text-base">Coupon Code</th>
              <th class="border-b p-2 text-sm md:text-base">Discount</th>
              <th class="border-b p-2 text-sm md:text-base">Status</th>
              <th class="border-b p-2 text-sm md:text-base">Valid Until</th>
              <th class="border-b p-2 text-sm md:text-base">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="coupon in filteredCoupons" :key="coupon.id">
              <td class="border-b p-2 text-center">{{ coupon.id }}</td>
              <td class="border-b p-2 text-center">{{ coupon.code }}</td>
              <td class="border-b p-2 text-center">{{ coupon.discount }}%</td>
              <td class="border-b p-2 text-center">{{ coupon.status }}</td>
              <td class="border-b p-2 text-center">{{ coupon.validUntil }}</td>
              <td class="border-b p-2 text-center">
                <button
                  @click="removeCoupon(coupon.id)"
                  class="px-3 py-1 bg-red-600 text-white rounded-md hover:bg-red-700 text-sm"
                >
                  Remove
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

        </main>
      </div>
    </div>
  </template>
  
  
  
  <script setup>

import { ref, computed } from 'vue';

// Define your coupon data (this would come from an API or localStorage in a real app)
const coupons = ref([
  { id: 'C001', code: 'SUMMER2025', discount: 10, status: 'active', validUntil: '2025-06-30' },
  { id: 'C002', code: 'WINTER2025', discount: 15, status: 'expired', validUntil: '2025-01-15' },
  { id: 'C003', code: 'NEWYEAR2025', discount: 20, status: 'active', validUntil: '2025-12-31' },
  // More coupons...
]);

// Filters state
const filters = ref({
  status: 'all',
  searchCode: '',
});

// New coupon form state
const newCoupon = ref({
  code: '',
  discount: '',
  status: 'active',
  validUntil: '',
});

// Filtered coupons based on selected filters
const filteredCoupons = computed(() => {
  return coupons.value.filter((coupon) => {
    const matchesStatus = filters.value.status === 'all' || coupon.status === filters.value.status;
    const matchesCode =
      !filters.value.searchCode || coupon.code.toLowerCase().includes(filters.value.searchCode.toLowerCase());

    return matchesStatus && matchesCode;
  });
});

// Function to handle adding a new coupon
const addCoupon = () => {
  if (
    !newCoupon.value.code ||
    !newCoupon.value.discount ||
    !newCoupon.value.validUntil ||
    isNaN(newCoupon.value.discount)
  ) {
    alert('Please fill in all fields with valid data.');
    return;
  }
  coupons.value.push({
    id: `C${Math.floor(Math.random() * 10000)}`, // Generate a random ID for the new coupon
    ...newCoupon.value,
  });
  // Reset form
  newCoupon.value = { code: '', discount: '', status: 'active', validUntil: '' };
};

// Function to handle removing a coupon
const removeCoupon = (couponId) => {
  const index = coupons.value.findIndex((coupon) => coupon.id === couponId);
  if (index !== -1) {
    coupons.value.splice(index, 1);
  }
};

// Function to handle export (simulated for now)
const exportCoupons = () => {
  console.log('Exporting coupons...');
  // You could trigger an export as a CSV or PDF here
  alert('Coupons exported!');
};
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


  </script>
  
  
  
  <style scoped>
  .min-h-screen {
    position: relative;
  }

  @keyframes twinkle {
    0%, 100% { opacity: 0; }
    50% { opacity: 1; }
  }

  .animate-twinkle {
    animation: twinkle 2s infinite;
  }
</style>