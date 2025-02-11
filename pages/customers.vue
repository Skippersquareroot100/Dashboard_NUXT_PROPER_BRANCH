
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
          

        <div class="container mx-auto p-6">
    <h1 class="text-3xl font-semibold mb-6">Customers - Active eCommerce CMS</h1>
    
    <!-- Search Bar -->
    <div class="mb-6">
      <input
        type="text"
        v-model="searchQuery"
        @input="searchCustomers"
        class="p-2 w-full border rounded-md"
        placeholder="Type email or name and press Enter"
      />
    </div>
    
    <!-- Customer Table -->
    <div class="overflow-x-auto bg-white rounded-lg shadow-md">
      <table class="min-w-full table-auto">
        <thead>
          <tr class="bg-gray-100 text-left">
            <th class="px-4 py-2">Name</th>
            <th class="px-4 py-2">Email Address</th>
            <th class="px-4 py-2">Phone</th>
            <th class="px-4 py-2">Package</th>
            <th class="px-4 py-2">Wallet Balance</th>
            <th class="px-4 py-2">Verification Status</th>
            <th class="px-4 py-2">Options</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="customer in paginatedCustomers" :key="customer.id" :class="{'bg-red-100': customer.status === 'blocked', 'bg-yellow-100': customer.status === 'suspicious'}">
            <td class="px-4 py-2">{{ customer.name }}</td>
            <td class="px-4 py-2">{{ customer.email }}</td>
            <td class="px-4 py-2">{{ customer.phone }}</td>
            <td class="px-4 py-2">{{ customer.package }}</td>
            <td class="px-4 py-2">{{ formatCurrency(customer.walletBalance) }}</td>
            <td class="px-4 py-2">{{ customer.verificationStatus }}</td>
            <td class="px-4 py-2">
              <button @click="viewCustomer(customer)" class="text-blue-500 hover:text-blue-700">View</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pagination -->
    <div class="mt-4 flex justify-center space-x-4">
      <button @click="previousPage" :disabled="currentPage <= 1" class="px-4 py-2 border rounded-md text-white bg-blue-500 hover:bg-blue-700 disabled:opacity-50">
        Previous
      </button>
      <span class="mx-2">Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage >= totalPages" class="px-4 py-2 border rounded-md text-white bg-blue-500 hover:bg-blue-700 disabled:opacity-50">
        Next
      </button>
    </div>
  </div>



      </main>
    </div>
  </div>
</template>



<script setup>
import { ref, computed } from 'vue'

// Menu items definition
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

// Reactive search query for menu items
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



// Sample customer data
const customers = ref([
  { id: 1, name: 'Hassaan', email: 'hassaan@example.com', phone: '+12015551025', package: 'Basic', walletBalance: 0, verificationStatus: 'Unverified', status: 'active' },
  { id: 2, name: 'yery castro', email: 'yery@example.com', phone: '+12015551025', package: 'Standard', walletBalance: 50, verificationStatus: 'Verified', status: 'active' },
  { id: 3, name: 'sajib', email: 'sajib@example.com', phone: '+12015551025', package: 'Premium', walletBalance: 120, verificationStatus: 'Verified', status: 'blocked' },
  { id: 4, name: 'Waleed Ahmed', email: 'waleed@example.com', phone: '+12015551025', package: 'Basic', walletBalance: 0, verificationStatus: 'Unverified', status: 'active' },
  { id: 5, name: 'David Elido', email: 'david@example.com', phone: '+12015551025', package: 'Standard', walletBalance: 30, verificationStatus: 'Verified', status: 'suspicious' },
  { id: 6, name: 'Test', email: 'test@example.com', phone: '+12015551025', package: 'Premium', walletBalance: 150, verificationStatus: 'Verified', status: 'active' },
])

// Filter customers based on search query
const filteredCustomers = computed(() => {
  if (!searchQuery.value) return customers.value
  return customers.value.filter(customer =>
    customer.name.toLowerCase().includes(searchQuery.value.toLowerCase()) || 
    customer.email.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})

// Pagination functionality
const currentPage = ref(1)
const itemsPerPage = 5
const totalPages = computed(() => Math.ceil(filteredCustomers.value.length / itemsPerPage))

const paginatedCustomers = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage
  const end = start + itemsPerPage
  return filteredCustomers.value.slice(start, end)
})

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--
  }
}

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++
  }
}

// Format currency for wallet balance
const formatCurrency = (value) => {
  return `$${value.toFixed(2)}`
}

// View customer details (this is just for example purposes)
const viewCustomer = (customer) => {
  console.log('Viewing customer:', customer)
}
</script>






<style scoped>
.container {
  max-width: 1200px;
}

input[type="text"] {
  padding: 0.5rem;
  width: 100%;
  border: 1px solid #ccc;
  border-radius: 0.25rem;
}

button {
  cursor: pointer;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  text-align: left;
  padding: 1rem;
}

th {
  background-color: #f3f4f6;
}

tr.bg-red-100 {
  background-color: #fee2e2;
}

tr.bg-yellow-100 {
  background-color: #fef9c3;
}
</style>


