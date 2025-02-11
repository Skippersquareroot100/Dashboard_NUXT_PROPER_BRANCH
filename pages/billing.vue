<template>
<div class="flex h-screen bg-gray-50">
    <!-- Sidebar -->
    <aside class="w-2/5 sm:w-64 bg-gray-900 text-white transition-all duration-300 flex-shrink-0 overflow-auto">
      <div class="p-2 sm:p-4">
        <div class="flex items-center gap-2 text-sm sm:text-xl font-bold">
          <div class="w-6 sm:w-8 h-6 sm:h-8 bg-red-500 rounded-lg"></div>
          <span class="ml-3">{{ shop.name ? shop.name + "'s" : 'ACTIVE' }}<span
              class="text-red-500">Platform</span></span>


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
          <router-link :to="item.path"
            class="flex items-center px-2 sm:px-4 py-2 sm:py-3 text-gray-300 hover:bg-gray-800">
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
            <!-- Circle for logo image -->
            <div class="flex items-center space-x-4">
              <router-link to="/manageShop" class="w-9 h-9 rounded-full overflow-hidden cursor-pointer">
                <img v-if="shop.logo" :src="shop.logo" alt="Shop Logo" class="w-full h-full object-cover" />
                <img v-else src="/avatar-placeholder.png" alt="Default Logo" class="w-full h-full object-cover" />
              </router-link>
            </div>
          </div>
        </div>
      </header>

      <!-- Dashboard Content -->
      <main class="p-4 mt-0.5">
    <div class="dashboard">
      <div v-if="!isLoggedIn" class="login-container">
        <h2>Welcome Back</h2>
        <form @submit.prevent="login" class="login-form">
          <div class="input-group">
            <input v-model="phoneNumber" type="text" placeholder="Phone Number" required>
            <span class="input-icon">📱</span>
          </div>
          <div class="input-group">
            <input v-model="password" type="password" placeholder="Password" required>
            <span class="input-icon">🔒</span>
          </div>
          <button type="submit" class="login-btn">Login</button>
        </form>
      </div>
      <div v-else class="dashboard-content">
        <header>
          <h1>Customer Dashboard</h1>
          <div class="profile-logout">
            <span class="profile-icon">👤</span>
            <button @click="logout" class="logout-btn">Logout</button>
          </div>
        </header>
        <div class="table-container">
          <table>
            <thead>
              <tr>
                <th>ID</th>
                <th>Phone Number</th>
                <th>Amount</th>
                <th>Status</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="customer in customers" :key="customer.id" class="customer-row">
                <td>{{ customer.id }}</td>
                <td>{{ customer.phone }}</td>
                <td>${{ customer.amount.toFixed(2) }}</td>
                <td>
                  <select 
                    v-model="customer.status" 
                    @change="updateStatus(customer)"
                    :class="{ 'status-due': customer.status === 'due', 'status-paid': customer.status === 'paid' }"
                  >
                    <option value="due">Due</option>
                    <option value="paid">Paid</option>
                  </select>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <transition name="toast">
        <div v-if="showToast" class="toast" :class="toastClass">
          {{ toastMessage }}
        </div>
      </transition>
    </div>
</main>
    </div>
  </div>
  </template>
  




<script setup>






import { ref, computed } from 'vue'





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

// Initialize shop as a reactive object
const shop = ref({ logo: null });
const shopUrl = ref("");

// Reactive search query
const searchQuery = ref("");
const filteredMenuItems = computed(() => {
  return menuItems.filter(item => item.name.toLowerCase().includes(searchQuery.value.toLowerCase()));
});

onMounted(() => {
  // Load shop data from localStorage
  const savedShop = localStorage.getItem("shopData");
  if (savedShop) {
    shop.value = JSON.parse(savedShop); // Populate shop data from localStorage
  }
  shopUrl.value = window.location.origin + "/shop"; // Adjust shop page link
});







const isLoggedIn = ref(false)
const phoneNumber = ref('')
const password = ref('')
const customers = ref([])
const showToast = ref(false)
const toastMessage = ref('')
const toastStatus = ref('')

const toastClass = computed(() => ({
  'toast-paid': toastStatus.value === 'paid',
  'toast-due': toastStatus.value === 'due'
}))

const login = () => {
  if (phoneNumber.value === '012345' && password.value === 'password') {
    isLoggedIn.value = true
    fetchCustomers()
  } else {
    showToastMessage('Invalid credentials', 'error')
  }
}

const logout = () => {
  isLoggedIn.value = false
  customers.value = []
}

const fetchCustomers = async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    const users = await response.json()
    customers.value = users.map(user => ({
      id: user.id,
      phone: user.phone,
      amount: Math.random() * 1000,
      status: Math.random() > 0.5 ? 'paid' : 'due'
    }))
  } catch (error) {
    console.error('Error fetching customers:', error)
    showToastMessage('Failed to fetch customers', 'error')
  }
}

const updateStatus = (customer) => {
  showToastMessage(`Status updated to ${customer.status} for customer ${customer.id}`, customer.status)
}

const showToastMessage = (message, status) => {
  toastMessage.value = message
  toastStatus.value = status
  showToast.value = true
  setTimeout(() => {
    showToast.value = false
  }, 3000)
}
</script>
  
  <style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap');
  
  .dashboard {
    font-family: 'Poppins', sans-serif;
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
    background-color: #f0f4f8;
    min-height: 100vh;
  }
  
  .login-container {
    max-width: 500px;
    margin: 100px auto;
    padding: 40px;
    background-color: white;
    border-radius: 10px;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease;
  }
  
  .login-container:hover {
    transform: translateY(-5px);
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.15);
  }
  
  .login-container h2 {
    text-align: center;
    margin-bottom: 30px;
    color: #333;
    font-weight: 600;
  }
  
  .login-form {
    display: flex;
    flex-direction: column;
    gap: 20px;
  }
  
  .input-group {
    position: relative;
  }
  
  .input-group input {
    width: 90%;
    padding: 15px;
    padding-left: 40px;
    border: 1px solid #e0e0e0;
    border-radius: 5px;
    font-size: 16px;
    transition: all 0.3s ease;
  }
  
  .input-group input:focus {
    border-color: #3498db;
    box-shadow: 0 0 0 2px rgba(52, 152, 219, 0.2);
  }
  
  .input-icon {
    position: absolute;
    left: 10px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 20px;
  }
  
  .login-btn {
    padding: 15px;
    background-color: #3498db;
    color: white;
    border: none;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
    transition: all 0.3s ease;
  }
  
  .login-btn:hover {
    background-color: #2980b9;
  }
  
  .dashboard-content {
    background-color: white;
    border-radius: 10px;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
    padding: 30px;
    animation: fadeInUp 0.5s ease-out;
  }
  
  @keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
  
  .dashboard-content header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 30px;
  }
  
  .dashboard-content h1 {
    color: #2c3e50;
    font-weight: 600;
  }
  
  .profile-logout {
    display: flex;
    align-items: center;
    gap: 15px;
  }
  
  .profile-icon {
    font-size: 24px;
    background-color: #3498db;
    color: white;
    width: 40px;
    height: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
  }
  
  .logout-btn {
    padding: 10px 20px;
    background-color: #e74c3c;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: all 0.3s ease;
  }
  
  .logout-btn:hover {
    background-color: #c0392b;
  }
  
  .table-container {
    overflow-x: auto;
  }
  
  table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 0 10px;
  }
  
  th, td {
    padding: 15px;
    text-align: left;
  }
  
  th {
    background-color: #f8f9fa;
    color: #2c3e50;
    font-weight: 600;
    text-transform: uppercase;
    font-size: 14px;
  }
  
  .customer-row {
    background-color: white;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
    transition: all 0.3s ease;
  }
  
  .customer-row:hover {
    transform: translateY(-3px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  }
  
  .customer-row td:first-child {
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
  }
  
  .customer-row td:last-child {
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
  }
  
  select {
    padding: 8px 12px;
    border-radius: 5px;
    border: 1px solid #e0e0e0;
    font-size: 14px;
    transition: all 0.3s ease;
  }
  
  .status-due {
    background-color: #ffebee;
    color: #c62828;
  }
  
  .status-paid {
    background-color: #e8f5e9;
    color: #2e7d32;
  }
  
  .toast {
    position: fixed;
    bottom: 20px;
    right: 20px;
    padding: 15px 25px;
    border-radius: 5px;
    color: white;
    font-weight: 500;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
    z-index: 1000;
  }
  
  .toast-paid {
    background-color: #2ecc71;
  }
  
  .toast-due {
    background-color: #e74c3c;
  }
  
  .toast-enter-active, .toast-leave-active {
    transition: all 0.5s ease;
  }
  
  .toast-enter-from, .toast-leave-to {
    opacity: 0;
    transform: translateY(50px);
  }
  
  @media (max-width: 768px) {
    .dashboard-content header {
      flex-direction: column;
      align-items: flex-start;
      gap: 15px;
    }
  
    .profile-logout {
      width: 100%;
      justify-content: space-between;
    }
  }
  </style>