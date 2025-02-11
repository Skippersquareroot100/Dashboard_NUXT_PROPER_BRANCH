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
      <h1 class="text-2xl font-bold">Orders</h1>
      <div class="flex items-center space-x-4">
        <button
          @click="exportOrders"
          class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700"
        >
          Export
        </button>
      </div>
    </div>
  </header>

  <!-- Filters Section -->
  <div class="flex flex-wrap gap-4 p-4 bg-white shadow-md">
    <!-- Select Order Type -->
    <div class="flex items-center space-x-2">
      <label for="orderType" class="text-sm">Select Order Type</label>
      <select
        id="orderType"
        v-model="filters.orderType"
        class="border border-gray-300 p-2 rounded-md text-sm"
      >
        <option value="all">All</option>
        <option value="physical">Physical</option>
        <option value="digital">Digital</option>
      </select>
    </div>

    <!-- Select Order Status -->
    <div class="flex items-center space-x-2">
      <label for="orderStatus" class="text-sm">Select Order Status</label>
      <select
        id="orderStatus"
        v-model="filters.orderStatus"
        class="border border-gray-300 p-2 rounded-md text-sm"
      >
        <option value="all">All</option>
        <option value="pending">Pending</option>
        <option value="completed">Completed</option>
        <option value="shipped">Shipped</option>
      </select>
    </div>

    <!-- Search Customer Phone -->
    <div class="flex items-center space-x-2">
      <label for="customerPhone" class="text-sm">Search Customer Phone</label>
      <input
        id="customerPhone"
        v-model="filters.customerPhone"
        type="text"
        placeholder="Enter phone number"
        class="border border-gray-300 p-2 rounded-md text-sm"
      />
    </div>
  </div>

  <!-- Orders Table -->
  <div class="p-4 overflow-auto">
    <table class="min-w-full table-auto border-collapse border border-gray-300">
      <thead class="bg-gray-200">
        <tr>
          <th class="border-b p-2">Order ID</th>
          <th class="border-b p-2">Customer Phone</th>
          <th class="border-b p-2">Order Type</th>
          <th class="border-b p-2">Status</th>
          <th class="border-b p-2">Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in filteredOrders" :key="order.id">
          <td class="border-b p-2">{{ order.id }}</td>
          <td class="border-b p-2">{{ order.customerPhone }}</td>
          <td class="border-b p-2">{{ order.orderType }}</td>
          <td class="border-b p-2">{{ order.status }}</td>
          <td class="border-b p-2">{{ order.total | currency }}</td>
        </tr>
      </tbody>
    </table>
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

// Define your order data (this would likely come from an API or localStorage in a real app)
const orders = ref([
  { id: '001', customerPhone: '1234567890', orderType: 'physical', status: 'pending', total: 99.99 },
  { id: '002', customerPhone: '0987654321', orderType: 'digital', status: 'completed', total: 49.99 },
  { id: '003', customerPhone: '5555555555', orderType: 'physical', status: 'shipped', total: 129.99 },
  { id: '004', customerPhone: '5555555555', orderType: 'physical', status: 'shipped', total: 129.99 },
  // More orders...
]);

// Filters state
const filters = ref({
  orderType: 'all',
  orderStatus: 'all',
  customerPhone: '',
});

// Filtered orders based on selected filters
const filteredOrders = computed(() => {
  return orders.value.filter((order) => {
    const matchesOrderType =
      filters.value.orderType === 'all' || order.orderType === filters.value.orderType;
    const matchesOrderStatus =
      filters.value.orderStatus === 'all' || order.status === filters.value.orderStatus;
    const matchesCustomerPhone =
      !filters.value.customerPhone || order.customerPhone.includes(filters.value.customerPhone);

    return matchesOrderType && matchesOrderStatus && matchesCustomerPhone;
  });
});

// Function to handle export (simulated for now)
const exportOrders = () => {
  console.log('Exporting orders...');
  // You could trigger an export as a CSV or PDF here
  alert('Orders exported!');
};

</script>


