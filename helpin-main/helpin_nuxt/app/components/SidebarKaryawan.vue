<template>
  <div>
    <div v-if="isOpen" @click="$emit('close')" class="fixed inset-0 bg-black/50 z-40 md:hidden backdrop-blur-sm"></div>

    <aside 
      :class="[
        'fixed inset-y-0 left-0 z-50 w-64 h-screen bg-gradient-to-b from-[#042400] to-[#0F8901] text-white flex flex-col justify-between transition-transform duration-300 ease-in-out md:static', 
        isOpen ? 'translate-x-0' : '-translate-x-full md:translate-x-0'
      ]"
    >
      
    
        <div class="flex items-center justify-center h-20 border-b border-white/10 mt-4 mb-2 shrink-0">
          <img 
            src="/assets/helpin_light_logo1.png" 
            alt="Logo Helpin" 
            class="h-10 w-auto object-contain hover:scale-105 transition-transform duration-300" 
          />
        </div>

         <div class="mb-6 shrink-0 -mt-4 relative z-10">
          <div class="px-6 md:px-8 py-5 bg-gradient-to-r from-[#0F8901] to-[#042400] shadow-md border-y border-white/5 relative overflow-hidden group">
            
            <div class="absolute inset-0 bg-white/5 opacity-0 group-hover:opacity-100 transition-opacity duration-300 pointer-events-none"></div>
            
            <div class="relative z-10">
              <div class="flex items-center gap-2 mb-2">
                <div class="w-2 h-2 rounded-full bg-blue-500 animate-pulse shadow-[0_0_8px_rgba(59,130,246,0.8)]"></div>
                <span class="text-[10px] font-semibold text-gray-300 uppercase tracking-widest">Pengguna Aktif</span>
              </div>
              
              <h2 class="text-xl font-bold mb-3 text-white truncate">Karyawan Suki</h2>
              
              <span class="inline-block px-3 py-1 text-[10px] font-black bg-green-500 text-white rounded uppercase tracking-wider shadow-sm">
               Karyawan
              </span>
            </div>
          </div>

        <nav class="flex flex-col pl-5 space-y-1 flex-1">
          <NuxtLink 
            v-for="menu in menus" 
            :key="menu.id" 
            :to="menu.path"
            @click="handleMenuClick"
            class="group relative w-full flex items-center gap-4 px-6 py-3.5 text-[15px] font-bold text-gray-300 rounded-l-full transition-all hover:text-white [&.router-link-active]:bg-[#F4FBF7] [&.router-link-active]:!text-[#19462D] [&.router-link-active]:shadow-[-5px_0_10px_rgba(0,0,0,0.1)]"
          >
            <div class="hidden group-[.router-link-active]:block absolute right-0 -top-[30px] w-[30px] h-[30px] bg-transparent rounded-br-[30px] shadow-[10px_10px_0_10px_#F4FBF7] pointer-events-none"></div>
            
            <component :is="menu.icon" class="w-5 h-5 flex-shrink-0 opacity-90 group-hover:scale-110 transition-transform group-[.router-link-active]:opacity-100" />
            <span>{{ menu.label }}</span>
            
            <div class="hidden group-[.router-link-active]:block absolute right-0 -bottom-[30px] w-[30px] h-[30px] bg-transparent rounded-tr-[30px] shadow-[10px_-10px_0_10px_#F4FBF7] pointer-events-none"></div>
          </NuxtLink>
        </nav>

        <div class="px-4 pb-4 mt-auto shrink-0">
          <div class="p-4 bg-[#143D25]/70 border border-white/5 rounded-2xl flex items-center justify-between shadow-lg backdrop-blur-md">
            <div class="min-w-0 flex-1 pr-2">
              <p class="text-[10px] text-white/60 flex items-center gap-2 mb-0.5">
                <span class="w-1.5 h-1.5 rounded-full bg-[#10B981]"></span> LOGGED IN AS
              </p>
              <h3 class="text-sm font-bold text-white m-0 truncate">Karyawan Suki</h3>
            </div>
            <button class="p-2.5 bg-red-500/10 hover:bg-red-500/20 rounded-xl transition text-red-400 hover:text-red-500 flex items-center justify-center shrink-0">
              <LogOutIcon class="w-5 h-5" />
            </button>
          </div>
        </div>

      </div>
    </aside>

  </div>
</template>

<script setup>
import { 
  LayoutDashboardIcon, UsersIcon, TruckIcon, PackageIcon, 
  ShoppingCartIcon, FileTextIcon, WalletIcon, LogOutIcon,
  RefreshCwIcon
} from 'lucide-vue-next'

defineProps({ isOpen: Boolean })
const emit = defineEmits(['close'])

const handleMenuClick = () => {
  if (window.innerWidth < 768) {
    emit('close')
  }
}

const menus = [
  { id: 'dashboard', label: 'Dashboard', path: '/panel_karyawan/dashboard_karyawan', icon: LayoutDashboardIcon },
  { id: 'pengguna', label: 'Pengguna', path: '/panel_karyawan/pengguna_karyawan', icon: UsersIcon },
  { id: 'supplier', label: 'Supplier', path: '/panel_karyawan/supplier_karyawan', icon: TruckIcon },
  { id: 'produk', label: 'Produk', path: '/panel_karyawan/produk_karyawan', icon: PackageIcon },
  { id: 'kasir', label: 'Kasir', path: '/panel_karyawan/kasir_karyawan', icon: ShoppingCartIcon },
  { id: 'transaksi', label: 'Transaksi', path: '/panel_karyawan/transaksi_karyawan', icon: FileTextIcon },
  { id: 'kas', label: 'Kas', path: '/panel_karyawan/kas_karyawan', icon: WalletIcon },
  { id: 'hybrid', label: 'Hybrid', path: '/panel_hybrid/dashboard', icon: RefreshCwIcon }
]
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
</style>