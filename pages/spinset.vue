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
       
           <!-- Dashboard Content -->
      <main class="p-4 mt-0.5">
        <div class="flex h-screen justify-center items-center bg-gradient-to-b from-red-800 to-red-950">
        
            <div class="flex flex-col items-center">
      <!-- Set Cooldown Time -->
      <div class="mt-4">
        <label class="block text-lg font-medium">Set Cooldown Time</label>
        <div class="flex gap-4">
          <!-- Hours Input -->
          <input
            v-model.number="cooldownHours"
            type="number"
            class="border rounded p-2 w-1/2"
            placeholder="Hours"
            min="0"
          />
          <!-- Minutes Input -->
          <input
            v-model.number="cooldownMinutes"
            type="number"
            class="border rounded p-2 w-1/2"
            placeholder="Minutes"
            min="0"
          />
        </div>
      </div>
  
      <!-- Submit and Reset Buttons -->
      <div class="flex gap-4 mt-4">
        <button @click="submitCooldown" class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-400">
          Submit
        </button>
        <button @click="resetInputs" class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-400">
          Reset
        </button>
      </div>
  
      <!-- Countdown Display -->
      <div class="mt-4">
        <p class="text-lg font-semibold">{{ countdown }}</p>
      </div>
    </div>


          <div class="min-h-screen bg-gradient-to-b from-red-800 to-red-950 flex flex-col items-center justify-center p-6 relative">
            
            <!-- Sparkles effect -->
            <div class="absolute inset-0 z-0">
              <div v-for="n in 12" :key="n" 
                   class="absolute w-1 h-1 bg-yellow-200 rounded-full animate-twinkle"
                   :style="`top: ${Math.random() * 100}%; left: ${Math.random() * 100}%; animation-delay: ${Math.random() * 2}s`">
              </div>
            </div>

            <div class="max-w-2xl w-full space-y-8">
              <!-- Main section with spinner and results -->
              <div class="flex flex-col justify-center items-center">
                <!-- Spin Wheel -->
                <div class="relative w-80 h-80 flex justify-center items-center">
                  <div
                    class="w-full h-full border-4 border-gray-300 rounded-full flex justify-center items-center"
                    :style="{ transform: `rotate(${rotation}deg)`, transition: spinning ? 'transform 3s ease-out' : 'none' }"
                  >
                    <svg viewBox="0 0 100 100" class="absolute w-full h-full">
                      <g v-for="(item, index) in items" :key="index" :transform="`rotate(${(360 / items.length) * index}, 50, 50)`">
                        <path
                          :d="`M50,50 L100,50 A50,50 0 0,1 50,0 Z`"
                          :fill="index % 2 === 0 ? '#FF0000' : '#FFD700'"
                          stroke="black"
                          stroke-width="0.5"
                        />
                        <text x="75" y="25" text-anchor="middle" transform="rotate(90, 75, 25)" class="text-xs fill-black">{{ item }}</text>
                      </g>
                    </svg>
                  </div>
                  <div class="absolute top-0 left-1/2 transform -translate-x-1/2 bg-[#FFD700] w-2 h-8"></div>
                </div>

                <!-- Spin Button and Result Boxes -->
                <div class="w-full max-w-xs mt-4 flex flex-col items-center">
                  <!-- Spin Button -->
                  <button 
                    @click="spin" 
                    class="bg-yellow-500 text-black px-4 py-2 rounded disabled:bg-gray-400 hover:bg-yellow-400 relative z-10" 
                    :disabled="spinsLeft === 0 || !canSpin"
                  >
                    Spin 
                  </button>
                </div>
                <div v-if="selectedItem" class="mt-4 text-lg font-bold text-yellow-300">
                 <!-- You won: {{ selectedItem }}-->
                 You won: {{ selectedRotationIndex+1 }}
                </div>
              </div>
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
  import Chart from "chart.js/auto";
  import { useStateStore } from '@/stores/useDataStore';
  
  const router = useRouter();
  
  // Initialize state from the store
  const Storestats = useStateStore();
  
  const stats = ref([]);
  const categories = ref([]);
  const brands = ref([]);
  
  
  // Initialize reactive variables for sales data
  const thisMonthSales = ref(0);
  const previousMonthSales = ref(0);
  const cashOnDelivery = ref(0);
  const cashOnStore = ref(0);
  const mobilePayment = ref(0);
  
  const loadSalesData = () => {
    const storedData = localStorage.getItem("salesData");
    return storedData ? JSON.parse(storedData) : null;
  };
  
  // On component mount, load and store the data
  onMounted(() => {
    const salesData = loadSalesData();
    
    if (salesData) {
      const thisMonth = salesData.thisMonth;
      const previousMonth = salesData.previousMonth;
  
      // Store the sales data in separate variables
      thisMonthSales.value = thisMonth.total || 0;
      previousMonthSales.value = previousMonth.total || 0;
  
      cashOnDelivery.value = thisMonth.cashOnDelivery || 0;
      cashOnStore.value = thisMonth.cashOnStore || 0;
      mobilePayment.value = thisMonth.mobilePayment || 0;
    }
  });
  
  
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
  
  // Sales Data
  const chartCanvas = ref(null);
  const thisMonthTotal = ref(0);
  const previousMonthTotal = ref(0);
  
  /*const loadSalesData = () => {
    const storedData = localStorage.getItem("salesData");
    return storedData ? JSON.parse(storedData) : null;
  };*/
  
  onMounted(() => {
  
  
    
  
    
  
    const salesData = loadSalesData();
  
    if (salesData && chartCanvas.value) {
      const thisMonth = salesData.thisMonth;
      const previousMonth = salesData.previousMonth;
  
      thisMonthTotal.value = thisMonthSales;
      previousMonthTotal.value = previousMonthSales;
  
      const chartData = {
        labels: ["Cash on Delivery", "Mobile Payment", "Other"],
        datasets: [
          {
            data: [thisMonth.cashOnDelivery, thisMonth.mobilePayment, thisMonth.cashOnStore],
            backgroundColor: ["#FF6384", "#36A2EB", "#FFCE56"],
            hoverBackgroundColor: ["#FF4567", "#3B85D1", "#FFB84F"],
          },
        ],
      };
  
      new Chart(chartCanvas.value, {
        type: "pie",
        data: chartData,
        options: {
          responsive: true,
          plugins: {
            tooltip: {
              callbacks: {
                label: (tooltipItem) => `$${tooltipItem.raw.toLocaleString()}`,
              },
            },
          },
        },
      });
    }
  });
  </script>
  