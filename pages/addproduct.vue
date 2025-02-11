
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
      <h1 class="text-3xl font-bold mb-4">Add New Product</h1>
  
      <!-- Form to Add Product -->
      <form @submit.prevent="submitForm" class="space-y-6 bg-white p-6 rounded-lg shadow-lg">
        <!-- Product Title -->
        <div>
          <label for="title" class="block text-lg font-medium">Product Title:</label>
          <input 
            type="text" 
            v-model="newProduct.title" 
            id="title" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter product title" />
        </div>
  
        <!-- Product Description -->
        <div>
          <label for="description" class="block text-lg font-medium">Product Description:</label>
          <textarea 
            v-model="newProduct.description" 
            id="description" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter product description"></textarea>
        </div>
  
        <!-- Product Price -->
        <div>
          <label for="price" class="block text-lg font-medium">Price:</label>
          <input 
            type="number" 
            v-model="newProduct.price" 
            id="price" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter product price" />
        </div>
  
        <!-- Product Rating -->
        <div>
          <label for="rating" class="block text-lg font-medium">Rating (out of 5):</label>
          <input 
            type="number" 
            v-model="newProduct.rating" 
            id="rating" 
            step="0.1" 
            min="0" 
            max="5" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter product rating" />
        </div>
  
        <!-- Product Stock -->
        <div>
          <label for="stock" class="block text-lg font-medium">Stock Quantity:</label>
          <input 
            type="number" 
            v-model="newProduct.stock" 
            id="stock" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter stock quantity" />
        </div>
  
        <!-- Product SKU -->
        <div>
          <label for="sku" class="block text-lg font-medium">SKU (Stock Keeping Unit):</label>
          <input 
            type="text" 
            v-model="newProduct.sku" 
            id="sku" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter product SKU" />
        </div>
  
        <!-- Product Image URL -->
        <div>
          <label for="image" class="block text-lg font-medium">Product Image URL:</label>
          <input 
            type="text" 
            v-model="newProduct.image" 
            id="image" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter product image URL" />
        </div>
  
        <!-- Product Category (Allow user to add custom category) -->
        <div>
          <label for="category" class="block text-lg font-medium">Category:</label>
          <div class="mt-2">
            <!-- Dropdown with pre-defined categories -->
            <select 
              v-if="!isCustomCategory"
              v-model="newProduct.category" 
              id="category" 
              class="w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500">
              <option value="Gaming">Gaming</option>
              <option value="PC Components">PC Components</option>
              <option value="Accessories">Accessories</option>
              <option value="Headsets">Headsets</option>
            </select>
  
            <!-- Input for custom category -->
            <div v-if="isCustomCategory" class="flex items-center space-x-2">
              <input 
                type="text" 
                v-model="newProduct.customCategory" 
                id="custom-category" 
                class="w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
                placeholder="Enter custom category name" />
              <button 
                type="button" 
                @click="toggleCustomCategory" 
                class="px-4 py-2 bg-red-600 text-white rounded-md hover:bg-red-700 focus:outline-none">
                Cancel
              </button>
            </div>
  
            <div class="mt-2 text-sm text-gray-600">
              <button 
                type="button" 
                @click="toggleCustomCategory" 
                class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none">
                {{ isCustomCategory ? 'Use Predefined Category' : 'Create Custom Category' }}
              </button>
            </div>
          </div>
        </div>
  
        <!-- Product Warranty -->
        <div>
          <label for="warranty" class="block text-lg font-medium">Warranty Information:</label>
          <input 
            type="text" 
            v-model="newProduct.warranty" 
            id="warranty" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter warranty details" />
        </div>
  
        <!-- Product Shipping Information -->
        <div>
          <label for="shipping" class="block text-lg font-medium">Shipping Information:</label>
          <input 
            type="text" 
            v-model="newProduct.shipping" 
            id="shipping" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter shipping details" />
        </div>
  
        <!-- Product Barcode -->
        <div>
          <label for="barcode" class="block text-lg font-medium">Barcode:</label>
          <input 
            type="text" 
            v-model="newProduct.barcode" 
            id="barcode" 
            required 
            class="mt-2 w-full p-3 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500" 
            placeholder="Enter barcode number" />
        </div>

        <!-- Product Image Upload -->

  
        <!-- Submit Button -->
        <router-link to="/products">
          <button 
            type="submit" 
            class="my-6 w-full px-6 py-3 bg-green-600 text-white rounded-md hover:bg-green-700 focus:outline-none">
            Add Product
          </button>
        </router-link>
      </form>
    </div>


 


      </main>
    </div>
  </div>
</template>



<script setup>
import { ref, computed, watch, onMounted } from "vue";

// Sidebar Menu Items
const menuItems = [
  { name: "Dashboard", path: "/dashboard", icon: "LayoutDashboard", active: true },
  { name: "Products", path: "/products", icon: "Package" },
  { name: "Orders", path: "/orders", icon: "ShoppingCart" },
  { name: "Customers", path: "/customers", icon: "Package" },
  { name: "Reports", path: "/reports", icon: "BarChart" },
  { name: "Manage Shop", path: "/manageShop", icon: "BarChart" },
  { name: "Coupon", path: "/coupon", icon: "BarChart" },
  { name: "Invoicing", path: "/invoicing", icon: "BarChart" },
  { name: "Lucky Spin", path: "/luckyspin", icon: "BarChart" },
  { name: "Billing", path: "/billing", icon: "BarChart" },
];

// Search functionality
const searchQuery = ref("");
const filteredMenuItems = computed(() => {
  return menuItems.filter(item =>
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
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


// Reactive state for new product form
const newProduct = ref({
  title: "",
  description: "",
  price: "",
  rating: "",
  stock: "",
  sku: "",
  image: "",
  category: "",
  customCategory: "",
  warranty: "",
  shipping: "",
  barcode: "",
});

// Reactive products array
const products = ref([]);

// Load products from localStorage (only on the client-side)
onMounted(() => {
  if (process.client) {
    const storedProducts = localStorage.getItem("products");
    products.value = storedProducts ? JSON.parse(storedProducts) : [];
  }
});

// Function to submit form and store data in localStorage
const submitForm = () => {
  // Add new product to the array
  products.value.push({ ...newProduct.value });

  // Save to localStorage (only if running in the client)
  if (process.client) {
    localStorage.setItem("products", JSON.stringify(products.value));
  }

  // Reset form
  newProduct.value = {
    title: "",
    description: "",
    price: "",
    rating: "",
    stock: "",
    sku: "",
    image: "",
    category: "",
    customCategory: "",
    warranty: "",
    shipping: "",
    barcode: "",
  };
};



// Watch for changes in products and update localStorage automatically
watch(products, (newVal) => {
  if (process.client) {
    localStorage.setItem("products", JSON.stringify(newVal));
  }
}, { deep: true });

// Handle category selection toggle
const isCustomCategory = ref(false);
const toggleCustomCategory = () => {
  isCustomCategory.value = !isCustomCategory.value;
  if (!isCustomCategory.value) newProduct.value.customCategory = "";
};
</script>
