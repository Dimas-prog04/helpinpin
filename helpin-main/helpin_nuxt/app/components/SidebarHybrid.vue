<template>
  <div>
    <div 
      v-if="isOpen" 
      @click="$emit('close')" 
      class="fixed inset-0 bg-black/60 z-30 md:hidden backdrop-blur-sm transition-opacity"
    ></div>

    <aside 
      :class="[
        'w-64 h-screen bg-gradient-to-b from-[#042400] to-[#0F8901] text-white flex flex-col justify-between shrink-0 fixed md:relative inset-y-0 left-0 z-40 transform transition-transform duration-300 h-screen',
        isOpen ? 'translate-x-0' : '-translate-x-full md:translate-x-0'
      ]"
    >
      <div class="flex flex-col h-full overflow-hidden">
        <div class="px-6 md:px-8 py-8 flex justify-between items-center shrink-0">
          <div class="flex items-center justify-center h-20 border-b border-white/10 mt-4 mb-2">
          <img 
            src="/assets/helpin_light_logo1.png" 
            alt="Logo Helpin" 
            class="h-10 w-auto object-contain hover:scale-105 transition-transform duration-300" 
          />
        </div>
          <button @click="$emit('close')" class="md:hidden text-white hover:text-red-400 transition">
            <XIcon class="w-6 h-6" />
          </button>
        </div>

        <div class="mb-6 shrink-0 -mt-4 relative z-10">
          <div class="px-6 md:px-8 py-5 bg-gradient-to-r from-[#0F8901] to-[#042400] shadow-md border-y border-white/5 relative overflow-hidden group">
            
            <div class="absolute inset-0 bg-white/5 opacity-0 group-hover:opacity-100 transition-opacity duration-300 pointer-events-none"></div>
            
            <div class="relative z-10">
              <div class="flex items-center gap-2 mb-2">
                <div class="w-2 h-2 rounded-full bg-blue-500 animate-pulse shadow-[0_0_8px_rgba(59,130,246,0.8)]"></div>
                <span class="text-[10px] font-semibold text-gray-300 uppercase tracking-widest">Pengguna Aktif</span>
              </div>
              
              <h2 class="text-xl font-bold mb-3 text-white truncate">Admin Suki SUPER</h2>
              
              <span class="inline-block px-3 py-1 text-[10px] font-black bg-green-500 text-white rounded uppercase tracking-wider shadow-sm">
               Karyawan
              </span>
            </div>
          </div>
        </div>

        <nav class="flex-1 overflow-y-auto no-scrollbar pl-4 space-y-1">
          <NuxtLink 
            v-for="menu in menus" 
            :key="menu.name" 
            :to="menu.path"
            :class="[
              'relative flex items-center gap-4 px-4 py-4 transition-all duration-300 group',
              activeMenu === menu.path
              ? 'active-menu bg-[#f4f7f5] text-[#1a402d] rounded-l-full font-black shadow-[-5px_0_10px_rgba(0,0,0,0.1)]' 
              : 'text-gray-400 hover:text-white font-bold hover:translate-x-1'
            ]"
          >
            <component :is="menu.icon" class="w-5 h-5 group-hover:scale-110 transition-transform" />
            <span class="tracking-tight">{{ menu.name }}</span>
          </NuxtLink>
        </nav>

        <div class="p-6 shrink-0 mt-auto">
          <div class="bg-[#143222] rounded-xl p-4 flex items-center justify-between border border-white/5 shadow-inner cursor-pointer hover:bg-red-500/20 transition-colors group">
            <div class="min-w-0 text-white">
              <span class="text-[10px] text-gray-500 group-hover:text-red-300 font-black uppercase block tracking-tighter transition-colors">Sistem Koperasi</span>
              <p class="text-sm font-bold truncate pr-2 group-hover:text-red-400 transition-colors">Tutup Shift & Keluar</p>
            </div>
            <LogOutIcon class="w-5 h-5 text-gray-400 group-hover:text-red-500 transition-colors" />
          </div>
        </div>
      </div>
    </aside>
  </div>
</template>

<script setup>
import { 
  MonitorIcon, WalletIcon, LayoutDashboardIcon, 
  XIcon, LogOutIcon 
} from 'lucide-vue-next'

defineProps({
  isOpen: Boolean,
  activeMenu: {
    type: String,
    default: ''
  }
})

defineEmits(['close'])

const menus = [
  { name: 'Kasir POS', icon: MonitorIcon, path: '/panel_hybrid/kasir' },
  { name: 'Buku Kas', icon: WalletIcon, path: '/panel_hybrid/kas' },
  { name: 'Kembali ke Dashboard', icon: LayoutDashboardIcon, path: '/' },
]
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

.active-menu::before {
  content: ""; position: absolute; right: 0; top: -24px; width: 24px; height: 24px;
  background-color: transparent; border-bottom-right-radius: 24px;
  box-shadow: 12px 12px 0 12px #f4f7f5; pointer-events: none;
}

.active-menu::after {
  content: ""; position: absolute; right: 0; bottom: -24px; width: 24px; height: 24px;
  background-color: transparent; border-top-right-radius: 24px;
  box-shadow: 12px -12px 0 12px #f4f7f5; pointer-events: none;
}
</style>