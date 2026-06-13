<template>
  <div class="flex h-screen w-full bg-[#F4FBF7] font-sans overflow-hidden">
    
    <SidebarKaryawan :isOpen="isSidebarOpen" @close="isSidebarOpen = false" />

    <main class="flex-1 flex flex-col h-full overflow-hidden w-full relative">
      
      <header class="flex justify-between items-center px-4 md:px-6 py-4 bg-white/90 backdrop-blur-md shadow-sm z-10 shrink-0 border-b border-gray-100">
        <div class="flex items-center gap-3">
          <button @click="isSidebarOpen = true" class="md:hidden p-2 -ml-2 text-gray-600 hover:bg-gray-100 rounded-lg transition">
            <MenuIcon class="w-6 h-6" />
          </button>
          <div>
            <h1 class="text-xl md:text-2xl font-black text-gray-800 tracking-tight">Kasir</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium">Dashboard dari sistem</p>
          </div>
        </div>
        <div class="bg-green-100 text-green-600 px-3 md:px-4 py-1.5 md:py-2 rounded-lg font-bold text-xs md:text-sm flex items-center gap-2">
          <div class="w-2 h-2 md:w-2.5 md:h-2.5 rounded-full bg-green-500 animate-pulse"></div>
          <span class="hidden sm:inline">STATUS ONLINE</span>
          <span class="sm:hidden">ONLINE</span>
        </div>
      </header>

      <div class="flex flex-col lg:flex-row flex-1 overflow-hidden p-4 md:p-6 gap-4 md:gap-6">
        
        <div class="flex-1 flex flex-col h-[50vh] lg:h-full overflow-hidden bg-transparent">
          
          <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-4 shrink-0 gap-3">
            <div class="flex gap-2 overflow-x-auto no-scrollbar w-full sm:w-auto pb-1 sm:pb-0">
              <button 
                v-for="cat in categories" :key="cat.id"
                @click="activeCategory = cat.id"
                :class="[
                  'px-4 md:px-5 py-2 rounded-full text-xs md:text-sm font-semibold whitespace-nowrap transition shadow-sm border',
                  activeCategory === cat.id 
                    ? 'bg-[#1b5e20] text-white border-[#1b5e20]' 
                    : 'bg-white border-gray-200 text-gray-600 hover:bg-gray-50'
                ]"
              >
                {{ cat.label }}
              </button>
            </div>
            
            <div class="relative w-full sm:w-72 shrink-0">
              <SearchIcon class="w-4 h-4 absolute left-4 top-1/2 -translate-y-1/2 text-gray-400" />
              <input 
                v-model="searchQuery"
                type="text" 
                placeholder="Cari Produk..." 
                class="w-full pl-10 pr-4 py-2 bg-white border border-gray-200 rounded-xl outline-none focus:border-[#1b5e20] text-sm font-medium transition shadow-sm"
              >
            </div>
          </div>

          <div class="flex-1 overflow-y-auto no-scrollbar pb-2 pr-1">
            <div class="grid grid-cols-2 md:grid-cols-3 xl:grid-cols-4 gap-3 md:gap-4">
              
              <div v-if="paginatedProducts.length === 0" class="col-span-full text-center py-10 text-gray-400 font-bold">
                Produk tidak ditemukan.
              </div>

              <div 
                v-for="product in paginatedProducts" :key="product.id"
                @click="addToCart(product)"
                class="bg-white border border-gray-200 rounded-xl p-2.5 md:p-3 flex flex-col hover:shadow-lg hover:border-[#1b5e20] transition-all group cursor-pointer relative"
              >
                <div class="absolute top-2 right-2 bg-white/90 backdrop-blur-sm text-[9px] md:text-[10px] font-black px-1.5 py-0.5 rounded-md z-10 border border-gray-100 uppercase tracking-wider"
                     :class="product.category === 'pangan' ? 'text-green-700' : 'text-orange-600'">
                  {{ product.category === 'pangan' ? 'Pangan' : 'Ternak' }}
                </div>
                
                <img :src="product.image" :alt="product.name" class="w-full h-24 md:h-28 object-contain mb-2 md:mb-3 rounded-lg mix-blend-multiply group-hover:scale-105 transition-transform duration-300">
                
                <div class="flex flex-col flex-1">
                  <p class="text-[9px] md:text-[10px] text-gray-400 font-bold mb-0.5 uppercase tracking-wider">{{ product.id }}</p>
                  <h4 class="text-xs md:text-sm font-bold text-gray-900 mb-1 leading-snug line-clamp-2">{{ product.name }}</h4>
                  <p class="text-[10px] md:text-[11px] text-gray-500 font-medium mb-2 md:mb-3">
                    Stok : <span :class="product.stock < 10 ? 'text-red-500' : 'text-green-600'" class="font-bold">{{ product.stock }} {{ product.unit }}</span>
                  </p>
                  
                  <div class="flex justify-between items-end mt-auto pt-1 border-t border-gray-50">
                    <span class="font-black text-[#1b5e20] text-xs md:text-sm">{{ formatRupiah(product.price) }}</span>
                    <button class="w-6 h-6 md:w-7 md:h-7 bg-gray-100 flex items-center justify-center rounded-md text-gray-500 group-hover:bg-[#1b5e20] group-hover:text-white transition-colors">
                      <PlusIcon class="w-3 h-3 md:w-4 md:h-4" />
                    </button>
                  </div>
                </div>
              </div>

            </div>
          </div>

          <div v-if="totalPages > 1" class="mt-3 pt-3 border-t border-gray-200 flex justify-between items-center shrink-0">
            <p class="hidden md:block text-xs font-bold text-gray-500">
              Menampilkan <span class="text-gray-900">{{ paginatedProducts.length }}</span> dari <span class="text-green-700 font-black">{{ filteredProducts.length }}</span> produk
            </p>
            
            <div class="flex items-center gap-1.5 md:gap-2 mx-auto md:mx-0">
              <button 
                @click="setPage(currentPage - 1)" 
                :disabled="currentPage === 1"
                class="px-3 py-1.5 rounded-lg text-xs font-bold transition-all border"
                :class="currentPage === 1 ? 'bg-gray-100 text-gray-400 border-gray-100 cursor-not-allowed' : 'bg-white text-gray-700 border-gray-200 hover:border-[#1b5e20] hover:text-[#1b5e20]'"
              >
                Prev
              </button>
              
              <div class="flex items-center gap-1">
                <button v-if="visiblePages[0] > 1" @click="setPage(1)" class="w-7 h-7 md:w-8 md:h-8 rounded-lg text-xs font-bold border bg-white text-gray-600 border-gray-200 hover:bg-green-50">1</button>
                <span v-if="visiblePages[0] > 2" class="text-gray-400 px-1 text-xs font-bold">...</span>

                <button 
                  v-for="page in visiblePages" :key="page"
                  @click="setPage(page)"
                  class="w-7 h-7 md:w-8 md:h-8 rounded-lg text-xs font-black transition-all border flex items-center justify-center"
                  :class="currentPage === page ? 'bg-[#1b5e20] text-white border-[#1b5e20] shadow-md' : 'bg-white text-gray-600 border-gray-200 hover:bg-green-50 hover:border-green-200'"
                >
                  {{ page }}
                </button>

                <span v-if="visiblePages[visiblePages.length - 1] < totalPages - 1" class="text-gray-400 px-1 text-xs font-bold">...</span>
                <button v-if="visiblePages[visiblePages.length - 1] < totalPages" @click="setPage(totalPages)" class="w-7 h-7 md:w-8 md:h-8 px-1 rounded-lg text-xs font-bold border bg-white text-gray-600 border-gray-200 hover:bg-green-50">{{ totalPages }}</button>
              </div>

              <button 
                @click="setPage(currentPage + 1)" 
                :disabled="currentPage === totalPages"
                class="px-3 py-1.5 rounded-lg text-xs font-bold transition-all border"
                :class="currentPage === totalPages ? 'bg-gray-100 text-gray-400 border-gray-100 cursor-not-allowed' : 'bg-white text-gray-700 border-gray-200 hover:border-[#1b5e20] hover:text-[#1b5e20]'"
              >
                Next
              </button>
            </div>
          </div>
          
        </div>

        <div class="w-full lg:w-[380px] shrink-0 bg-white/50 backdrop-blur-sm border border-gray-200 rounded-3xl p-4 md:p-5 flex flex-col h-[50vh] lg:h-full shadow-sm">
          
          <div class="flex justify-between items-center mb-4 shrink-0">
            <h3 class="text-sm font-black text-gray-800 flex items-center gap-2">
              <ReceiptIcon class="w-5 h-5 text-[#1b5e20]" /> Draft Transaksi
            </h3>
            <button @click="clearCart" class="text-xs font-bold text-red-500 hover:text-red-700 hover:bg-red-50 px-2 py-1 rounded transition">Kosongkan</button>
          </div>

          <div class="flex-1 overflow-y-auto no-scrollbar space-y-2 md:space-y-3 mb-4 md:mb-6">
            <div v-if="cart.length === 0" class="h-full flex flex-col items-center justify-center text-gray-400">
              <ShoppingCartIcon class="w-10 h-10 mb-2 opacity-20" />
              <p class="text-xs font-bold">Keranjang kosong</p>
            </div>

            <div 
              v-for="item in cart" :key="item.id" 
              class="bg-white border border-gray-100 rounded-xl p-2 md:p-3 flex gap-2 md:gap-3 shadow-sm hover:border-gray-200 transition-colors"
            >
              <img :src="item.image" class="w-10 h-10 md:w-12 md:h-12 rounded-lg object-cover border border-gray-100 shrink-0 hidden sm:block" alt="item">
              <div class="flex-1 flex flex-col justify-center min-w-0">
                <h4 class="text-[11px] md:text-xs font-bold text-gray-800 leading-tight mb-1 truncate">{{ item.name }}</h4>
                <div class="flex justify-between items-center mt-1">
                  <p class="text-[10px] md:text-xs text-green-600 font-bold">
                    {{ formatRupiah(item.price) }} <span class="text-gray-900 font-black ml-0.5 text-[9px] md:text-[10px]">x {{ item.qty }}</span>
                  </p>
                  <p class="text-[11px] md:text-xs text-[#1b5e20] font-black">{{ formatRupiah(item.price * item.qty) }}</p>
                </div>
                <div class="flex items-center gap-2 mt-2">
                  <button @click="updateQty(item, -1)" class="w-5 h-5 flex items-center justify-center bg-gray-100 rounded text-gray-600 hover:text-red-500 font-bold text-xs">-</button>
                  <span class="text-[10px] font-black w-3 text-center">{{ item.qty }}</span>
                  <button @click="updateQty(item, 1)" class="w-5 h-5 flex items-center justify-center bg-gray-100 rounded text-gray-600 hover:text-green-600 font-bold text-xs">+</button>
                </div>
              </div>
            </div>
          </div>

          <div class="bg-white border border-gray-200 p-4 rounded-2xl shrink-0 shadow-sm">
            <div class="space-y-1.5 mb-3">
              <div class="flex justify-between text-xs md:text-sm">
                <span class="text-gray-500 font-medium">Subtotal</span>
                <span class="text-gray-800 font-bold">{{ formatRupiah(subtotal) }}</span>
              </div>
              <div class="flex justify-between text-xs md:text-sm">
                <span class="text-gray-500 font-medium">Pajak (2%)</span>
                <span class="text-gray-800 font-bold">{{ formatRupiah(tax) }}</span>
              </div>
            </div>
            <div class="flex justify-between items-center border-t border-gray-100 pt-3 mb-4">
              <span class="text-sm font-bold text-gray-800">Total</span>
              <span class="text-lg md:text-xl font-black text-gray-900">{{ formatRupiah(total) }}</span>
            </div>
            <button 
              :disabled="cart.length === 0"
              @click="openPaymentModal"
              class="w-full py-3 rounded-xl font-bold flex items-center justify-center gap-2 transition-all shadow-md text-sm md:text-base"
              :class="cart.length === 0 ? 'bg-gray-300 text-gray-500 cursor-not-allowed shadow-none' : 'bg-[#1b5e20] hover:bg-[#154618] text-white hover:-translate-y-0.5'"
            >
              <WalletIcon class="w-4 h-4 md:w-5 md:h-5" /> Pembayaran
            </button>
          </div>

        </div>

      </div>
    </main>

    <div v-if="isPaymentModalOpen" class="fixed inset-0 z-[100] flex items-center justify-center bg-black/60 backdrop-blur-sm p-4">
      <div class="bg-white rounded-2xl shadow-2xl w-full max-w-md overflow-hidden flex flex-col animate-fade">
        <div class="bg-[#1b5e20] p-4 md:p-5 flex justify-between items-center text-white shrink-0">
          <h3 class="text-lg font-black flex items-center gap-2"><WalletIcon class="w-5 h-5" /> Pembayaran</h3>
          <button @click="closePaymentModal" class="text-white/70 hover:text-white transition p-1 rounded-lg hover:bg-white/10"><XIcon class="w-5 h-5" /></button>
        </div>
        <div class="p-5 md:p-6 overflow-y-auto max-h-[70vh] no-scrollbar">
          <div class="bg-gray-50 rounded-xl p-4 space-y-2 mb-5 border border-gray-100">
            <div class="flex justify-between items-end border-b border-gray-200 pb-3 mb-1">
              <span class="text-xs font-black text-gray-500 uppercase tracking-widest">Total Tagihan</span>
              <span class="text-2xl font-black text-[#1b5e20]">{{ formatRupiah(total) }}</span>
            </div>
            <div class="flex justify-between text-xs text-gray-500 font-bold pt-2">
              <span>Item Dibeli</span>
              <span>{{ cart.length }} Item</span>
            </div>
          </div>
          <div class="space-y-2">
            <label class="text-xs font-black text-gray-700 block uppercase tracking-wide">Nominal Uang Diterima</label>
            <div class="relative">
              <span class="absolute left-4 top-1/2 -translate-y-1/2 font-black text-gray-500 text-lg">Rp</span>
              <input v-model="formattedAmountTendered" type="text" class="w-full pl-12 pr-4 py-3 md:py-4 bg-white border-2 border-gray-200 rounded-xl font-black text-lg md:text-xl text-gray-800 focus:outline-none focus:border-[#1b5e20] focus:ring-4 focus:ring-green-500/20 transition-all" placeholder="0" />
            </div>
          </div>
          <div class="mt-4 p-3 md:p-4 rounded-xl flex justify-between items-center transition-colors duration-300 border" :class="changeAmount >= 0 ? 'bg-green-50 border-green-200 text-green-800' : 'bg-red-50 border-red-200 text-red-800'">
            <span class="font-black text-xs md:text-sm uppercase tracking-wide">Kembalian</span>
            <span class="font-black text-lg md:text-xl">{{ amountTendered === null || amountTendered === 0 ? 'Rp 0' : (changeAmount >= 0 ? formatRupiah(changeAmount) : 'Uang Kurang') }}</span>
          </div>
        </div>
        <div class="p-4 md:p-5 border-t border-gray-100 bg-gray-50 flex gap-3 shrink-0">
          <button @click="closePaymentModal" class="px-5 py-3 font-bold text-gray-600 bg-white border-2 border-gray-200 rounded-xl hover:bg-gray-50 transition text-sm">Batal</button>
          <button @click="confirmPayment" :disabled="changeAmount < 0 || !amountTendered" class="flex-1 py-3 font-black text-white text-sm md:text-base tracking-wide rounded-xl shadow-lg transition-all flex items-center justify-center" :class="changeAmount < 0 || !amountTendered ? 'bg-gray-300 cursor-not-allowed shadow-none' : 'bg-[#1b5e20] hover:bg-[#154618] hover:-translate-y-0.5'">Selesaikan Transaksi</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { 
  MenuIcon, SearchIcon, PlusIcon, ReceiptIcon, WalletIcon, 
  ShoppingCartIcon, XIcon
} from 'lucide-vue-next'

import SidebarAdmin from '~/components/SidebarAdmin.vue'

const isSidebarOpen = ref(false)

// ==========================================
// DATA PRODUK (100.000 DATA SINTETIS)
// ==========================================
const categories = ref([
  { id: 'semua', label: 'Semua Produk' },
  { id: 'pangan', label: 'Agro Pangan' },
  { id: 'ternak', label: 'Hasil Ternak' }
])
const activeCategory = ref('semua')
const searchQuery = ref('')

const baseProducts = [
  { name: 'Menir Jagung Super Halus', category: 'pangan', price: 23000, stock: 45, unit: 'Kg', image: 'https://images.unsplash.com/photo-1586201375761-83865001e31c?auto=format&fit=crop&w=400&q=80' },
  { name: 'Beras Premium Mapan', category: 'pangan', price: 75000, stock: 120, unit: 'Sak', image: 'https://images.unsplash.com/photo-1551754655-cd27e38d2076?auto=format&fit=crop&w=400&q=80' },
  { name: 'Telur Ayam Omega 3 Pilihan', category: 'ternak', price: 32000, stock: 8, unit: 'Peti', image: 'https://images.unsplash.com/photo-1506976785307-8732e854ad03?auto=format&fit=crop&w=400&q=80' },
  { name: 'Susu Sapi Segar Literan', category: 'ternak', price: 18000, stock: 15, unit: 'Liter', image: 'https://images.unsplash.com/photo-1563636619-e9143da7973b?auto=format&fit=crop&w=400&q=80' },
  { name: 'Cabai Rawit Merah', category: 'pangan', price: 45000, stock: 25, unit: 'Kg', image: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR5Obf9469jpUOyLMX3Nk6kXXYc2-1ajlcVOA&s' },
  { name: 'Pupuk Kompos Koperasi', category: 'pangan', price: 25000, stock: 200, unit: 'Karung', image: 'https://images.unsplash.com/photo-1464226184884-fa280b87c399?auto=format&fit=crop&w=400&q=80' },
]

// Generate 100.000 Data Dummy
const products = ref(
  Array.from({ length: 1000 }).map((_, index) => {
    const base = baseProducts[index % baseProducts.length];
    const prefix = base.category === 'pangan' ? 'PGN' : 'TRN';
    return {
      ...base,
      id: `${prefix}-${String(index + 1).padStart(6, '0')}`,
    }
  })
)

// ==========================================
// PAGINATION & FILTER LOGIC (ANTI-JEBOL)
// ==========================================
const currentPage = ref(1)
const itemsPerPage = ref(8) 

const filteredProducts = computed(() => {
  return products.value.filter(p => {
    const matchCat = activeCategory.value === 'semua' || p.category === activeCategory.value
    const matchSearch = p.name.toLowerCase().includes(searchQuery.value.toLowerCase()) || 
                        p.id.toLowerCase().includes(searchQuery.value.toLowerCase())
    return matchCat && matchSearch
  })
})

const totalPages = computed(() => Math.ceil(filteredProducts.value.length / itemsPerPage.value))

// Membatasi tombol pagination agar tidak melebar kalau datanya ada ratusan ribu
const visiblePages = computed(() => {
  const pages = []
  const maxVisible = 5 // Jumlah angka yang ditampilkan
  
  if (totalPages.value <= maxVisible) {
    for (let i = 1; i <= totalPages.value; i++) pages.push(i)
  } else {
    let start = Math.max(currentPage.value - 2, 1)
    let end = Math.min(start + maxVisible - 1, totalPages.value)
    
    if (end === totalPages.value) {
      start = Math.max(end - maxVisible + 1, 1)
    }
    
    for (let i = start; i <= end; i++) pages.push(i)
  }
  return pages
})

const paginatedProducts = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value
  const end = start + itemsPerPage.value
  return filteredProducts.value.slice(start, end)
})

watch([searchQuery, activeCategory], () => {
  currentPage.value = 1
})

const setPage = (page) => {
  if(page >= 1 && page <= totalPages.value) {
    currentPage.value = page
  }
}

// ==========================================
// LOGIKA KERANJANG & PEMBAYARAN
// ==========================================
const cart = ref([])

const addToCart = (product) => {
  if (product.stock <= 0) return alert('Stok produk habis!')
  const existing = cart.value.find(item => item.id === product.id)
  if (existing) {
    if (existing.qty < product.stock) existing.qty++
  } else {
    cart.value.push({ ...product, qty: 1 })
  }
}

const updateQty = (item, amount) => {
  const index = cart.value.findIndex(i => i.id === item.id)
  if (index === -1) return
  const targetItem = cart.value[index]
  const newQty = targetItem.qty + amount
  if (newQty <= 0) cart.value.splice(index, 1) 
  else if (newQty <= targetItem.stock) targetItem.qty = newQty
}

const clearCart = () => cart.value = []

const subtotal = computed(() => cart.value.reduce((sum, item) => sum + (item.price * item.qty), 0))
const tax = computed(() => subtotal.value * 0.02)
const total = computed(() => subtotal.value + tax.value)

const formatRupiah = (value) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(value)

const isPaymentModalOpen = ref(false)
const amountTendered = ref(null)

const formattedAmountTendered = computed({
  get() { return !amountTendered.value ? '' : amountTendered.value.toLocaleString('id-ID') },
  set(newValue) {
    const cleanValue = newValue.replace(/\D/g, '')
    amountTendered.value = cleanValue ? parseInt(cleanValue, 10) : null
  }
})

const changeAmount = computed(() => (Number(amountTendered.value) || 0) - total.value)

const openPaymentModal = () => {
  if (cart.value.length === 0) return
  amountTendered.value = null 
  isPaymentModalOpen.value = true
}

const closePaymentModal = () => {
  isPaymentModalOpen.value = false
  amountTendered.value = null
}

const confirmPayment = () => {
  if (changeAmount.value < 0) return 
  alert('Transaksi Berhasil! (Data terkirim, cek console log)')
  clearCart()
  closePaymentModal()
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.2s ease-out; }
@keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
</style>