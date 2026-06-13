<template>
  <div>
    <div 
      v-if="isOpen" 
      @click="$emit('close')" 
      class="fixed inset-0 bg-black/60 z-30 md:hidden backdrop-blur-sm transition-opacity"
    ></div>

    <aside 
      :class="[
        'w-[280px] h-screen bg-gradient-to-b from-[#042400] to-[#0F8901] text-white flex flex-col justify-between shrink-0 fixed md:relative inset-y-0 left-0 z-40 transform transition-transform duration-300 shadow-xl',
        isOpen ? 'translate-x-0' : '-translate-x-full md:translate-x-0'
      ]"
    >
      <div class="flex flex-col h-full overflow-hidden py-6">
        
        <div class="px-8 mb-10 flex items-center justify-between shrink-0">
          <div class="flex items-center mt-2 mb-2">
            <img 
              src="/assets/helpin_light_logo1.png" 
              alt="Logo Helpin" 
              class="h-10 w-auto object-contain hover:scale-105 transition-transform duration-300" 
            />
          </div>
          
          <button @click="$emit('close')" class="md:hidden text-gray-300 hover:text-white transition">
            <XIcon class="w-6 h-6" />
          </button>
        </div>

        <div class="px-8 mb-8 shrink-0">
          <div class="flex items-center gap-2 mb-1">
            <div class="w-1.5 h-1.5 bg-green-400 rounded-full animate-pulse"></div>
            <p class="text-green-300 text-[10px] font-bold tracking-wider uppercase">Pengguna Aktif</p>
          </div>
          <h2 class="text-white text-lg font-bold mb-2">Auditor Suki</h2>
          <span class="bg-white/10 text-white text-[10px] px-3 py-1 rounded border border-white/20 uppercase font-bold tracking-wider backdrop-blur-sm">
            Auditor
          </span>
        </div>

        <nav 
          ref="sidebarNav"
          @scroll="handleSidebarScroll"
          class="flex-1 overflow-y-auto no-scrollbar pl-4 space-y-1"
        >
          <template v-for="menu in menus" :key="menu.name">
            <NuxtLink 
              :to="menu.path"
              @click="handleMenuClick"
              :class="[
                'relative flex items-center gap-4 px-6 py-3.5 transition-all duration-300 group rounded-l-full',
                isActive(menu.path)
                ? 'active-menu font-bold shadow-[-5px_0_15px_rgba(0,0,0,0.1)]' 
                : 'text-gray-300 hover:text-white font-medium hover:translate-x-1'
              ]"
            >
              <component :is="menu.icon" class="w-5 h-5 transition-transform group-hover:scale-110" />
              <span class="tracking-tight text-sm">{{ menu.name }}</span>
            </NuxtLink>
          </template>
        </nav>

        <div class="px-6 mt-4 shrink-0">
          <div class="bg-black/20 backdrop-blur-sm rounded-xl p-4 flex items-center justify-between border border-white/5">
            <div class="overflow-hidden">
              <div class="flex items-center gap-1.5 mb-1">
                <div class="w-1.5 h-1.5 bg-green-400 rounded-full animate-pulse"></div>
                <p class="text-green-300 text-[9px] font-bold tracking-widest uppercase">Logged in as</p>
              </div>
              <p class="text-white text-xs font-bold truncate pr-2">Admin Suki SUPER</p>
            </div>
            <button class="text-red-400 hover:text-red-300 hover:scale-110 transition shrink-0 p-1">
              <LogOutIcon class="w-5 h-5" />
            </button>
          </div>
        </div>

      </div>
    </aside>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, nextTick } from 'vue'
import { useRoute } from 'vue-router'
import { useState } from '#app'
import { LayoutDashboardIcon, XIcon, LogOutIcon } from 'lucide-vue-next'

const props = defineProps({ isOpen: Boolean })
const emit = defineEmits(['close'])

const route = useRoute()

// Kunci utama penahan posisi scroll di simpan di state global Nuxt
const sidebarNav = ref(null)
const savedScrollTop = useState('adminSidebarScrollPosition', () => 0)

// Fungsi untuk merekam posisi scroll secara real-time saat discroll oleh user
const handleSidebarScroll = (event) => {
  savedScrollTop.value = event.target.scrollTop
}

// Kembalikan posisi scroll tepat setelah komponen dimuat di layar
onMounted(() => {
  if (sidebarNav.value) {
    sidebarNav.value.scrollTop = savedScrollTop.value
  }
})

// Dengar perubahan rute jalan, paksa posisi container tetep ngestay di koordinat pixel terakhir
watch(() => route.path, () => {
  nextTick(() => {
    if (sidebarNav.value) {
      sidebarNav.value.scrollTop = savedScrollTop.value
    }
  })
})

// HANYA MENYISAKAN FITUR DASHBOARD
const menus = [
  { name: 'Dashboard', icon: LayoutDashboardIcon, path: '/panel_auditore/dashboard_auditore' }
]

const isActive = (path) => {
  return route.path === path || route.path === path.toLowerCase();
}

const handleMenuClick = () => {
  if (window.innerWidth < 768) {
    emit('close')
  }
}
</script>

<style scoped>
/* Sembunyikan Scrollbar biar rapi seperti desain */
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

/* WARNA MENU AKTIF DIPINDAH KE SINI AGAR 100% KEBACA BROWSER */
.active-menu {
  background-color: #f4fbf7;
  /* Sesuaikan warna text saat menu aktif agar matching dengan tema hijau tua gradasi */
  color: #0F8901; 
}

/* Efek Lengkung (Curved) menu yang aktif menyatu dengan background konten abu-abu */
.active-menu::before {
  content: ""; 
  position: absolute; 
  right: 0; 
  top: -24px; 
  width: 24px; 
  height: 24px;
  background-color: transparent; 
  border-bottom-right-radius: 24px;
  box-shadow: 12px 12px 0 12px #f4fbf7; 
  pointer-events: none;
}

.active-menu::after {
  content: ""; 
  position: absolute; 
  right: 0; 
  bottom: -24px; 
  width: 24px; 
  height: 24px;
  background-color: transparent; 
  border-top-right-radius: 24px;
  box-shadow: 12px -12px 0 12px #f4fbf7; 
  pointer-events: none;
}
</style>