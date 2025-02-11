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
<!-- Set Cooldown Time -->
<div class="mt-4">
  <label for="cooldownTime" class="block text-lg font-medium">Set Cooldown Time</label>
  <input
    type="time"
    id="cooldownTime"
    v-model="cooldownTime"
    class="border rounded p-2 w-full bg-white"
  />
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

         <!-- Spinner Item Box with Click to Edit -->
         <div class="w-full h-full flex justify-center items-center">
              <div class="grid grid-cols-4 gap-4">
                <div
                  v-for="(item, index) in items"
                  :key="index"
                  class="relative flex justify-center items-center p-2 border-2 border-gray-300 rounded-md cursor-pointer hover:bg-gray-200"
                  @click="openEditModal(index)"
                >
                  {{ item }}
                </div>
              </div>
            </div>

            <!-- Modal for Editing Item Names -->
            <div v-if="isModalOpen" class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
              <div class="bg-white p-6 rounded shadow-md max-w-sm w-full">
                <label for="item-name" class="block text-lg font-medium">Edit Item Name</label>
                <input id="item-name" v-model="newItemName" class="border rounded p-2 w-full" />
                <div class="mt-4 flex justify-end gap-4">
                  <button @click="closeModal" class="bg-gray-300 text-black px-4 py-2 rounded">Cancel</button>
                  <button @click="saveItemName" class="bg-blue-500 text-white px-4 py-2 rounded">Save</button>
                </div>
              </div>
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
import { ref, onMounted } from 'vue';
import axios from 'axios';


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

  // Reactive search query
  const searchQuery = ref("");
  const filteredMenuItems = computed(() => {
    return menuItems.filter(item => item.name.toLowerCase().includes(searchQuery.value.toLowerCase()));
  });
  





  const isModalOpen = ref(false);
const selectedItemIndex = ref(null);
const newItemName = ref('');

// Open the modal and set the item to be edited
const openEditModal = (index) => {
  selectedItemIndex.value = index;
  newItemName.value = items.value[index]; // Set the current item name
  isModalOpen.value = true;
};

// Close the modal without saving changes
const closeModal = () => {
  isModalOpen.value = false;
  newItemName.value = '';
};

// Save the new name and update the items array
const saveItemName = () => {
  if (selectedItemIndex.value !== null && newItemName.value.trim() !== '') {
    items.value[selectedItemIndex.value] = newItemName.value;
    // Save updated items to localStorage
    localStorage.setItem("items", JSON.stringify(items.value));
    saveToServer();
    closeModal();
  }


  
};






// LocalStorage Keys
const SPIN_KEY = "spin_timestamp";
const COOLDOWN_KEY = "cooldown_duration";




// State Variables
const cooldownHours = ref(0);
const cooldownMinutes = ref(0);
const lastSpinTime = ref(null);
const canSpin = ref(true);
const countdown = ref("");
const cooldownActive = ref(false); // Prevents re-submitting cooldown

// Shop Data
const shop = ref({ name: "", logo: "" });




const submitCooldown = () => {
  if (cooldownActive.value) return; // Prevent submitting while cooldown is active

  if (!cooldownTime.value) {
    console.error("No cooldown time selected");
    return;
  }

  const [hours, minutes] = cooldownTime.value.split(":").map(Number); // Extract HH:MM
  const now = new Date();
  const selectedTime = new Date(now);
  selectedTime.setHours(hours, minutes, 0, 0); // Set the cooldown end time to today

  // If selected time is in the past, set it for the next day
  if (selectedTime <= now) {
    selectedTime.setDate(selectedTime.getDate() + 1); // Move to the next day
  }

  // Calculate the exact timestamp when cooldown expires
  const cooldownEndTime = selectedTime.getTime();

  // Store the exact cooldown end time in localStorage
  localStorage.setItem("cooldownEndTime", cooldownEndTime);

  canSpin.value = false;
  cooldownActive.value = true;
  
  // Start countdown using the stored cooldown end time
  startCountdown(cooldownEndTime - Date.now());
};





onMounted(() => {
  const savedShop = localStorage.getItem("shopData");
  if (savedShop) {
    shop.value = JSON.parse(savedShop);

  }

  const savedItems = localStorage.getItem("items");
  if (savedItems) {
    items.value = JSON.parse(savedItems);
  } 

  loadFromLocalStorage();




    // Restore cooldown timer if it was set before refresh
  const cooldownEndTime = localStorage.getItem("cooldownEndTime");

if (cooldownEndTime) {
  const timeLeft = cooldownEndTime - Date.now();

  if (timeLeft > 0) {
    canSpin.value = false;
    cooldownActive.value = true;
    startCountdown(timeLeft);
  } else {
    canSpin.value = true;
    cooldownActive.value = false;
    countdown.value = "Ready to Spin!";
  }
}


});

// Items for Spin Wheel
const items = ref(["Item-1", "Item-2", "Item-3", "Item-4", "Item-5", "Item-6", "Item-7", "Item-8"]);
const rotation = ref(0);
const spinning = ref(false);
const selectedItem = ref(null);

// Load data from localStorage
const loadFromLocalStorage = () => {
  lastSpinTime.value = localStorage.getItem(SPIN_KEY);
  const savedCooldown = localStorage.getItem(COOLDOWN_KEY);

  if (savedCooldown) {
    const { hours, minutes } = JSON.parse(savedCooldown);
    cooldownHours.value = hours;
    cooldownMinutes.value = minutes;
  }

  checkSpinAvailability();
};

// Save data to localStorage
const saveToLocalStorage = () => {
  localStorage.setItem(SPIN_KEY, lastSpinTime.value);
  localStorage.setItem(
    COOLDOWN_KEY,
    JSON.stringify({ hours: cooldownHours.value, minutes: cooldownMinutes.value })
  );
};

// Check Spin Availability
const checkSpinAvailability = () => {
  if (!lastSpinTime.value) return;

  const lastSpinTimestamp = parseInt(lastSpinTime.value);
  const cooldownMs = (cooldownHours.value * 60 * 60 * 1000) + (cooldownMinutes.value * 60 * 1000);
  const currentTime = Date.now();
  const timeLeft = lastSpinTimestamp + cooldownMs - currentTime;

  if (timeLeft > 0) {
    canSpin.value = false;
    cooldownActive.value = true; // Prevents changing cooldown while active
    startCountdown(timeLeft);
  } else {
    canSpin.value = true;
    cooldownActive.value = false; // Re-enables cooldown input
    countdown.value = "Ready to Spin!";
  }
};











// Start Countdown Timer
const startCountdown = (timeLeftMs) => {
  canSpin.value = false;
  cooldownActive.value = true;

  const endTime = Date.now() + timeLeftMs;

  const interval = setInterval(() => {
    const remainingTime = endTime - Date.now();

    if (remainingTime <= 0) {
      clearInterval(interval);
      canSpin.value = true;
      cooldownActive.value = false;
      countdown.value = "Ready to Spin!";
    } else {
      const hours = Math.floor(remainingTime / (1000 * 60 * 60));
      const minutes = Math.floor((remainingTime % (1000 * 60 * 60)) / (1000 * 60));
      const seconds = Math.floor((remainingTime % (1000 * 60)) / 1000);
      countdown.value = `${hours}h ${minutes}m ${seconds}s left`;
    }
  }, 1000);
};







// Rotation Values Array
const rotationValues = [2475, 2430, 2385, 2340, 2295, 2250, 2205, 2160];

// Declare refs to store the selected rotation and its index
const selectedRotation = ref(0);
const selectedRotationIndex = ref(0);

const randomIndex = Math.floor(Math.random() * rotationValues.length);

// Store the random number and its index

selectedRotation.value = rotationValues[randomIndex];
selectedRotationIndex.value = randomIndex;














// Handle Spin
const spin = () => {
  if (spinning.value || !canSpin.value) return;

  spinning.value = true;
  const randomSpin = Math.floor(1800 + Math.random() * 1800);
  rotation.value =selectedRotation.value;

  setTimeout(() => {
    spinning.value = false;
    const totalRotation = rotation.value % 360;
    const segmentSize = 360 / items.value.length;
    const index = Math.floor((totalRotation + segmentSize / 2) / segmentSize) % items.value.length;
    selectedItem.value = items.value[index];

    // Save Spin Timestamp
    lastSpinTime.value = Date.now();
    saveToLocalStorage();
    //checkSpinAvailability();
  }, 3000);

  countdown.value="press submit button";
};

// Submit Cooldown Time


const saveToServer = async () => {
  try {
    const response = await axios.post('/your-api-endpoint', {
      items: items.value,  // Send the array from localStorage
    });
    console.log('Items saved successfully:', response.data);
  } catch (error) {
    console.error('Error saving items:', error);
  }
};













</script>





<style scoped>
/* Additional styling can be added here */
.text-xs {
  font-size: 5px;
}
.fill-black {
  fill: black;
}

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
