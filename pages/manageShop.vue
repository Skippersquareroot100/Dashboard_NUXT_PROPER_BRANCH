
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
        <div class="min-h-screen bg-white p-6">
      <div class="max-w-2xl mx-auto bg-white shadow-lg rounded-lg p-6 border border-gray-200">
        <h2 class="text-3xl font-semibold text-blue-500 text-center mb-6">🛍 Manage Shop</h2>
  
        <!-- Success Message -->
        <p v-if="message" class="text-green-700 font-medium text-center bg-green-100 p-2 rounded mb-4">
          {{ message }}
        </p>
  
        <div class="grid grid-cols-1 gap-4">
          <!-- Business ID (Read-only) -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Business ID</label>
            <input v-model="shop.businessId" type="text" class="w-full border-gray-300 p-2 rounded bg-gray-100" readonly />
          </div>
  
          <!-- Business Name -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Business Name</label>
            <input v-model="shop.name" type="text"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900" />
          </div>
  
          <!-- Business Type -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Business Type</label>
            <input v-model="shop.type" type="text"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900" />
          </div>
  
          <!-- Shop Email -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Shop Email</label>
            <input v-model="shop.email" type="email"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900" />
          </div>
  
          <!-- Phone Number -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Shop Phone Number</label>
            <input v-model="shop.phone" type="text"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900" />
          </div>
  
          <!-- Country -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Country</label>
            <input v-model="shop.country" type="text"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900" />
          </div>
  
          <!-- Address -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Shop Address</label>
            <input v-model="shop.address" type="text"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900" />
          </div>
  
          <!-- Topbar Announcement -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Topbar Announcement</label>
            <textarea v-model="shop.announcement"
              class="w-full border p-2 rounded focus:ring-2 focus:ring-blue-500 bg-gray-100 text-gray-900"></textarea>
          </div>
  
          <!-- Logo Upload -->
          <div>
            <label class="block text-sm font-medium text-gray-600">Upload Logo</label>
            <input type="file" @change="handleLogoUpload" class="w-full border p-2 rounded bg-gray-100" />
            <div v-if="shop.logo" class="mt-2">
              <img :src="shop.logo" alt="Shop Logo" class="w-24 h-24 rounded-md object-cover border shadow-md" />
            </div>
          </div>
  
          <!-- QR Code -->
          <div class="text-center mt-6">
            <p class="text-sm text-gray-600 mb-2">Shop QR Code</p>
            <div class="p-3 bg-gray-100 rounded-lg border shadow-md inline-block">
              <QrcodeVue :value="shopUrl" size="140" class="mx-auto" />
            </div>
          </div>
  
          <!-- Update Button -->
          <button @click="updateShopInfo"
            class="w-full bg-gradient-to-r from-red-500 to-red-600 text-white py-2 mt-4 rounded-lg hover:shadow-xl transition transform hover:scale-105">
            Update Shop Info
          </button>
        </div>
      </div>
    </div>


      </main>
    </div>
  </div>
</template>


<script setup>
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";
import QrcodeVue from "qrcode.vue";

/* ---------------------- Sidebar Menu Data ---------------------- */
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

/* ---------------------- Sidebar Search Functionality ---------------------- */
const searchQuery = ref("");
const filteredMenuItems = computed(() =>
  menuItems.filter((item) =>
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
);

/* ---------------------- Shop Data State ---------------------- */
const shop = ref({
  businessId: "72792",
  name: "Akever",
  type: "Clothing & Apparel",
  email: "hasanmaruf0055@gmail.com",
  phone: "01400551001",
  country: "Bangladesh",
  address: "Dhaka",
  announcement: "",
  logo: "",
});

const shopUrl = ref(""); // Shop page URL
const message = ref(""); // Success message

// Create router instance
const router = useRouter();

/* ---------------------- Load Data from Local Storage on Mount ---------------------- */
onMounted(() => {
  const savedShop = localStorage.getItem("shopData");
  if (savedShop) {
    shop.value = JSON.parse(savedShop); // Populate shop data from localStorage
  }
  shopUrl.value = window.location.origin + "/shop"; // Adjust shop page link
});

/* ---------------------- Handle Logo Upload ---------------------- */
const handleLogoUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      shop.value.logo = e.target.result; // Save as Base64
      // Store the updated shop data in localStorage
      localStorage.setItem("shopData", JSON.stringify(shop.value));
    };
    reader.readAsDataURL(file);
  }
};

/* ---------------------- Update Shop Info ---------------------- */
const updateShopInfo = () => {
  // Confirm the action before proceeding
  if (confirm("Are you sure you want to update the shop info?")) {
    // Update localStorage only if confirmed
    localStorage.setItem("shopData", JSON.stringify(shop.value));
    message.value = "✅ Shop information updated successfully!";
    
    // Redirect to dashboard after a short delay
    setTimeout(() => {
      message.value = "";
      router.push("/dashboard");
    }, 500);
  }
};
</script>

