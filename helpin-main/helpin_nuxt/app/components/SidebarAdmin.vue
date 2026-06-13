<template>
  <div>
    <div 
      v-if="isOpen" 
      @click="$emit('close')" 
      class="fixed inset-0 bg-black/60 z-30 md:hidden backdrop-blur-sm transition-opacity"
    ></div>

    <aside 
      :class="[
        'w-64 h-screen bg-gradient-to-b from-[#042400] to-[#0F8901] text-white flex flex-col justify-between shrink-0 fixed md:relative inset-y-0 left-0 z-40 transform transition-transform duration-300',
        isOpen ? 'translate-x-0' : '-translate-x-full md:translate-x-0'
      ]"
    >
      <div class="flex flex-col h-full overflow-hidden">
        
        <div class="px-6 md:px-4 py-2 flex justify-between items-center shrink-0">
          <div class="flex items-center justify-center h-20 border-b border-white/10 mt-4 mb-2 w-full">
            <img 
              src="/assets/helpin_light_logo1.png" 
              alt="Logo Helpin" 
              class="h-10 w-auto object-contain hover:scale-105 transition-transform duration-300" 
            />
          </div>
          <button @click="$emit('close')" class="md:hidden text-white hover:text-red-400 transition absolute right-4 top-8">
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
                Admin
              </span>
            </div>
          </div>
        </div>

        <nav class="flex-1 overflow-y-auto no-scrollbar pl-4 space-y-1 mb-4">
          <template v-for="menu in menus" :key="menu.name">
            
            <NuxtLink 
              v-if="!menu.sub"
              :to="menu.path"
              @click="handleMenuClick"
              class="group relative flex items-center gap-4 px-4 py-3.5 transition-all duration-300 text-gray-300 hover:text-white font-bold rounded-l-full [&.router-link-active]:bg-[#F4FBF7] [&.router-link-active]:text-[#19462D] [&.router-link-active]:shadow-[-5px_0_10px_rgba(0,0,0,0.1)]"
            >
              <div class="hidden group-[.router-link-active]:block absolute right-0 -top-[24px] w-[24px] h-[24px] bg-transparent rounded-br-[24px] shadow-[12px_12px_0_12px_#F4FBF7] pointer-events-none"></div>
              
              <component :is="menu.icon" class="w-5 h-5 group-hover:scale-110 transition-transform" />
              <span class="tracking-tight text-sm">{{ menu.name }}</span>

              <div class="hidden group-[.router-link-active]:block absolute right-0 -bottom-[24px] w-[24px] h-[24px] bg-transparent rounded-tr-[24px] shadow-[12px_-12px_0_12px_#F4FBF7] pointer-events-none"></div>
            </NuxtLink>

            <div v-else class="flex flex-col">
              <button 
                @click="isAkunOpen = !isAkunOpen"
                class="relative flex items-center justify-between w-full px-4 py-3.5 text-gray-300 hover:text-white font-bold transition-all"
              >
                <div class="flex items-center gap-4">
                  <component :is="menu.icon" class="w-5 h-5" />
                  <span class="tracking-tight text-sm">{{ menu.name }}</span>
                </div>
                <ChevronDownIcon :class="['w-4 h-4 transition-transform', isAkunOpen ? 'rotate-180' : '']" />
              </button>
              
              <div v-show="isAkunOpen" class="pl-8 flex flex-col space-y-1 mt-1">
                <NuxtLink 
                  v-for="sub in menu.sub" :key="sub.name"
                  :to="sub.path"
                  @click="handleMenuClick"
                  class="group relative flex items-center gap-3 px-4 py-2.5 transition-all duration-300 text-gray-400 hover:text-white font-semibold rounded-l-full [&.router-link-exact-active]:bg-[#F4FBF7] [&.router-link-exact-active]:text-[#19462D] [&.router-link-exact-active]:font-black"
                >
                  <component :is="sub.icon" class="w-4 h-4 group-hover:scale-110 transition-transform" />
                  <span class="text-xs">{{ sub.name }}</span>
                </NuxtLink>
              </div>
            </div>
            
          </template>
        </nav>

        <div class="px-4 pb-4 mt-auto shrink-0">
          <div class="p-4 bg-[#143D25]/70 border border-white/5 rounded-2xl flex items-center justify-between shadow-lg backdrop-blur-md">
            <div class="min-w-0 flex-1 pr-2">
              <span class="text-[10px] text-gray-400 font-bold uppercase block tracking-wider mb-0.5">Logged In As</span>
              <p class="text-sm font-bold text-white truncate">Admin Suki</p>
            </div>
            <button class="text-red-400 hover:text-red-500 hover:scale-105 bg-red-500/10 p-2.5 rounded-xl transition shadow-sm flex items-center justify-center shrink-0">
              <LogOutIcon class="w-5 h-5" />
            </button>
          </div>
        </div>

      </div>
    </aside>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { 
  LayoutDashboardIcon, UsersIcon, UserCircleIcon, UserIcon, 
  TruckIcon, BoxIcon, ShoppingCartIcon, ReceiptIcon, 
  WalletIcon, ActivityIcon, StoreIcon, XIcon, LogOutIcon, ChevronDownIcon,
  RefreshCwIcon
} from 'lucide-vue-next'

const props = defineProps({ isOpen: Boolean })
const emit = defineEmits(['close'])

const isAkunOpen = ref(true)

const menus = [
  { name: 'Dashboard', icon: LayoutDashboardIcon, path: '/panel_admin/dashboard_admin' },
  { 
    name: 'Akun', icon: UsersIcon, 
    sub: [
      { name: 'Karyawan', icon: UserIcon, path: '/panel_admin/karyawan_admin' },
      { name: 'Pengguna', icon: UserCircleIcon, path: '/panel_admin/Pengguna_admin' }
    ]
  },
  { name: 'Supplier', icon: TruckIcon, path: '/panel_admin/Supplier_admin' },
  { name: 'Produk', icon: BoxIcon, path: '/panel_admin/Produk_admin' },
  { name: 'Kasir', icon: ShoppingCartIcon, path: '/panel_admin/Kasir_admin' },
  { name: 'Transaksi', icon: ReceiptIcon, path: '/panel_admin/Transaksi_admin' },
  { name: 'Kas', icon: WalletIcon, path: '/panel_admin/Kas_admin' },
  { name: 'Aktifitas', icon: ActivityIcon, path: '/panel_admin/Aktifitas_admin' },
  { name: 'Custom Ecomerce', icon: StoreIcon, path: '/panel_admin/Ecommerce_admin' },
  { name: 'Hybrid', icon: RefreshCwIcon, path: '/panel_hybrid/dashboard' }
]

const handleMenuClick = () => {
  if (window.innerWidth < 768) {
    emit('close')
  }
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
</style>