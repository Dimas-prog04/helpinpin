<template>
  <div class="flex h-screen w-full bg-[#F4FBF7] font-sans overflow-hidden relative">
    
    <SidebarKaryawan :isOpen="isSidebarOpen" @close="isSidebarOpen = false" />

    <main class="flex-1 flex flex-col overflow-y-auto relative w-full no-scrollbar">
      <header class="flex justify-between items-center px-6 md:px-10 py-5 border-b border-gray-200 bg-white/90 backdrop-blur-md shadow-sm z-10 sticky top-0">
        <div class="flex items-center gap-4">
          <button @click="isSidebarOpen = true" class="md:hidden p-2 -ml-2 text-gray-600 hover:bg-gray-100 rounded-lg transition">
            <MenuIcon class="w-6 h-6" />
          </button>
          <div>
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Katalog Produk</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Manajemen inventaris dan monitoring AI</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw] animate-fade">
        
        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex flex-col sm:flex-row gap-4 mb-6">
            <div class="relative flex-1">
              <SearchIcon class="w-5 h-5 absolute left-4 top-1/2 -translate-y-1/2 text-gray-400" />
              <input v-model="searchQuery" type="text" placeholder="Cari Produk..." class="w-full pl-12 pr-4 py-3 border border-gray-200 rounded-xl outline-none focus:border-[#19462D] bg-gray-50 text-sm">
            </div>
            <select v-model="activeCategory" class="w-full sm:w-48 p-3 border border-gray-200 rounded-xl outline-none focus:border-[#19462D] bg-gray-50 text-sm font-semibold text-gray-600">
              <option value="">Semua Kategori</option>
              <option value="makanan">Makanan</option>
              <option value="minuman">Minuman</option>
            </select>
          </div>
          
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6 mb-6">
            <div v-if="paginatedProducts.length === 0" class="col-span-full text-center py-10 text-gray-400 font-bold">
              Produk tidak ditemukan.
            </div>

            <div v-for="item in paginatedProducts" :key="item.id" class="border border-gray-200 rounded-2xl p-4 text-center hover:shadow-lg transition bg-white group cursor-pointer relative overflow-hidden">
              <img :src="item.image" alt="Product" class="w-full h-32 object-contain mb-4 rounded-xl mix-blend-multiply group-hover:scale-105 transition-transform">
              <h4 class="text-base font-black text-[#19462D] mb-3 truncate">{{ item.nama }}</h4>
              <div class="text-[11px] text-gray-500 text-left space-y-1 mb-4 bg-gray-50 p-3 rounded-xl border border-gray-100">
                <p class="flex justify-between"><span>Kategori:</span> <strong class="text-gray-800 capitalize">{{ item.kategori }}</strong></p>
                <p class="flex justify-between"><span>Harga Beli:</span> <strong class="text-gray-800">{{ formatRupiah(item.hargaBeli) }}</strong></p>
                <p class="flex justify-between"><span>Harga Jual:</span> <strong class="text-green-600">{{ formatRupiah(item.hargaJual) }}</strong></p>
                <p class="flex justify-between"><span>Stok:</span> <strong class="text-gray-800">{{ item.stok }}</strong></p>
                <p class="flex justify-between"><span>Expired:</span> <strong class="text-red-500">{{ item.expired }}</strong></p>
              </div>
              <div class="flex justify-between items-center">
                <p class="text-[9px] font-bold text-gray-400 uppercase tracking-wider truncate">Masuk: {{ item.tanggalMasuk }}</p>
                <button @click="openDetail(item)" class="text-[10px] bg-green-50 text-green-700 px-2 py-1 rounded-md font-bold hover:bg-green-100 transition">Detail</button>
              </div>
            </div>
          </div>

          <div v-if="totalPages > 1" class="flex justify-center gap-2 mb-6">
            <button 
              v-for="page in totalPages" :key="page"
              @click="setPage(page)"
              class="w-10 h-10 flex items-center justify-center rounded-xl font-bold text-sm transition shadow-sm"
              :class="currentPage === page ? 'bg-[#19462D] text-white shadow-md' : 'bg-gray-50 border border-gray-200 text-gray-600 hover:bg-gray-100'"
            >
              {{ page }}
            </button>
          </div>

          <button @click="showProdukModal = true" class="w-full bg-[#19462D] hover:bg-[#113620] text-white py-4 rounded-xl font-bold flex items-center justify-center gap-2 transition shadow-lg shadow-green-900/20 text-sm uppercase tracking-wider">
            <PackagePlusIcon class="w-5 h-5" /> Tambah Produk
          </button>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2 mb-6">
            <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Monitoring Produk AI
          </h3>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Kode Produk</th>
                  <th class="p-4 font-black">Nama Produk</th>
                  <th class="p-4 font-black">Jenis</th>
                  <th class="p-4 font-black">Stok</th>
                  <th class="p-4 font-black">Informasi</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in monitoringData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.kode || item.id }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4 font-medium text-gray-600">{{ item.jenis || item.kategori }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.stok }}</td>
                  <td class="p-4 font-bold" :class="item.isRed ? 'text-red-500' : 'text-gray-600'">{{ item.info }}</td>
                  <td class="p-4 text-center">
                    <button @click="openDetail(item)" class="w-8 h-8 rounded-lg bg-blue-500 text-white flex items-center justify-center hover:bg-blue-600 mx-auto transition shadow-sm">
                      <EyeIcon class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </section>

      </div>
    </main>

    <div v-if="showProdukModal" class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-black/50 backdrop-blur-sm transition-opacity" @click.self="showProdukModal = false">
      <div class="bg-white rounded-2xl w-full max-w-3xl shadow-xl overflow-hidden animate-fade max-h-[90vh] flex flex-col">
        <div class="flex justify-between items-center p-6 border-b border-gray-100 shrink-0">
          <h2 class="text-xl font-bold text-[#19462D]">Tambah Produk</h2>
          <button @click="showProdukModal = false" class="text-gray-400 hover:text-red-500"><XIcon class="w-6 h-6" /></button>
        </div>
        <div class="p-6 bg-gray-50/50 overflow-y-auto flex-1">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
            <div><label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Nama Produk</label><input type="text" placeholder="Contoh : Mie Goreng" class="w-full p-3 border border-gray-200 rounded-xl bg-white text-sm focus:outline-none focus:border-[#19462D]"></div>
            <div><label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Harga Jual</label><input type="text" placeholder="Rp 23.000" class="w-full p-3 border border-gray-200 rounded-xl bg-white text-sm focus:outline-none focus:border-[#19462D]"></div>
            <div><label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Harga Beli</label><input type="text" placeholder="Rp 20.000" class="w-full p-3 border border-gray-200 rounded-xl bg-white text-sm focus:outline-none focus:border-[#19462D]"></div>
            <div>
              <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Kategori</label>
              <select class="w-full p-3 border border-gray-200 rounded-xl bg-white text-sm focus:outline-none focus:border-[#19462D]">
                <option>Pilih Kategori</option>
                <option>Makanan</option>
                <option>Minuman</option>
              </select>
            </div>
          </div>
          <button class="w-full py-4 border-2 border-dashed border-[#19462D] text-[#19462D] font-bold rounded-xl hover:bg-green-50 transition mb-6 flex justify-center items-center gap-2">
            <ImageIcon class="w-5 h-5" /> Masukkan Gambar Produk
          </button>
          <button @click="showProdukModal = false" class="w-full px-8 py-4 bg-[#19462D] text-white font-bold rounded-xl hover:bg-[#113620] transition uppercase tracking-wider text-sm shadow-md">Simpan Produk</button>
        </div>
      </div>
    </div>

    <div v-if="showDetailModal" class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-black/50 backdrop-blur-sm transition-opacity" @click.self="showDetailModal = false">
      <div class="bg-white rounded-[24px] w-full max-w-sm p-6 text-center shadow-xl animate-fade relative">
        <button @click="showDetailModal = false" class="absolute top-4 right-4 text-gray-400 hover:text-red-500 transition">
          <XIcon class="w-5 h-5" />
        </button>
        
        <h3 class="text-lg font-black text-[#19462D] mb-4 uppercase tracking-widest border-b border-gray-100 pb-3">Informasi Produk</h3>
        
        <img :src="selectedItem.image || 'https://images.unsplash.com/photo-1604147706283-d7119b5b822c?auto=format&fit=crop&q=80&w=200&h=150'" class="w-full h-32 object-contain mb-4 rounded-xl mix-blend-multiply bg-gray-50 border border-gray-100 p-2">
        
        <h4 class="text-xl font-black text-gray-800 mb-1 leading-snug">{{ selectedItem.nama }}</h4>
        <p class="text-sm font-bold text-green-600 mb-5">{{ selectedItem.hargaJual ? formatRupiah(selectedItem.hargaJual) : 'Lihat Sistem Utama' }}</p>
        
        <div class="text-xs text-left bg-gray-50 p-4 rounded-xl space-y-3 mb-6 border border-gray-100 font-medium">
          <p class="flex justify-between items-center text-gray-600 border-b border-gray-100 pb-2"><span>Kode SKU:</span> <strong class="text-gray-900 font-black">{{ selectedItem.kode || selectedItem.id }}</strong></p>
          <p class="flex justify-between items-center text-gray-600 border-b border-gray-100 pb-2"><span>Kategori:</span> <strong class="text-gray-900 capitalize">{{ selectedItem.jenis || selectedItem.kategori }}</strong></p>
          <p class="flex justify-between items-center text-gray-600 border-b border-gray-100 pb-2"><span>Sisa Stok:</span> <strong class="text-gray-900">{{ selectedItem.stok }}</strong></p>
          <p class="flex justify-between items-center text-gray-600"><span>Status Expired:</span> <strong class="text-red-500">{{ selectedItem.expired || selectedItem.info }}</strong></p>
        </div>
        
        <button @click="showDetailModal = false" class="w-full py-3.5 bg-gradient-to-r from-[#19462D] to-[#123320] text-white font-black rounded-xl hover:opacity-90 transition shadow-lg shadow-green-900/20 uppercase tracking-widest text-sm">
          Tutup Detail
        </button>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { MenuIcon, SearchIcon, EyeIcon, PackagePlusIcon, ImageIcon, XIcon } from 'lucide-vue-next'
import SidebarAdmin from '~/components/SidebarAdmin.vue'

const isSidebarOpen = ref(false)

// State Modal
const showProdukModal = ref(false)
const showDetailModal = ref(false)
const selectedItem = ref({}) // Data item yang sedang di-klik untuk detail

// State Filter & Pagination
const searchQuery = ref('')
const activeCategory = ref('')
const currentPage = ref(1)
const itemsPerPage = ref(8) // Menampilkan 8 item per halaman

// ==========================================
// 1. DATA KATALOG (30 Data Dummy)
// ==========================================
const baseNames = ['Mie Sedap Goreng', 'Indomie Ayam Bawang', 'Kopi Kapal Api', 'Teh Pucuk Harum', 'Susu Beruang', 'Roti Aoka', 'Minyak Goreng Bimoli', 'Kecap Sania', 'Gula Gulaku', 'Beras Maknyus']
const products = ref(
  Array.from({ length: 30 }).map((_, i) => ({
    id: `PRD-${100 + i}`,
    nama: baseNames[i % baseNames.length] + ` Edisi ${i + 1}`,
    kategori: i % 2 === 0 ? 'makanan' : 'minuman',
    hargaBeli: 2000 + (i * 200),
    hargaJual: 3000 + (i * 300),
    stok: `${10 + (i * 2)} Pcs`,
    expired: `05-04-202${6 + (i % 3)}`,
    tanggalMasuk: `01-02-202${5 + (i % 2)}`,
    image: 'https://images.unsplash.com/photo-1604147706283-d7119b5b822c?auto=format&fit=crop&q=80&w=200&h=150'
  }))
)

// Logika Pencarian & Filter Kategori
const filteredProducts = computed(() => {
  return products.value.filter(p => {
    const matchCategory = activeCategory.value === '' || p.kategori === activeCategory.value;
    const matchSearch = p.nama.toLowerCase().includes(searchQuery.value.toLowerCase());
    return matchCategory && matchSearch;
  })
})

// Logika Pagination (Halaman)
const totalPages = computed(() => Math.ceil(filteredProducts.value.length / itemsPerPage.value))

const paginatedProducts = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return filteredProducts.value.slice(start, end);
})

// Jika sedang ngetik pencarian atau ganti kategori, paksa halaman kembali ke 1
watch([searchQuery, activeCategory], () => {
  currentPage.value = 1
})

const setPage = (page) => {
  currentPage.value = page
}

// ==========================================
// 2. DATA MONITORING AI (Tabel)
// ==========================================
const monitoringData = ref([
  { kode: 'TX-076-01', nama: 'Mie Sedap Goreng', jenis: 'Makanan', stok: '30 PCS', info: '3 Hari Lagi Expired', isRed: true, image: 'https://images.unsplash.com/photo-1604147706283-d7119b5b822c?auto=format&fit=crop&q=80&w=200&h=150' },
  { kode: 'DD-987-37', nama: 'Pasta Gigi Closeup', jenis: 'Cosmetic', stok: '5 PCS', info: 'Stok Menipis', isRed: true, image: 'https://images.unsplash.com/photo-1604147706283-d7119b5b822c?auto=format&fit=crop&q=80&w=200&h=150' },
  { kode: 'TY-055-45', nama: 'Selai Kacang', jenis: 'Makanan', stok: '7 PCS', info: '7 Hari Lagi Expired', isRed: true, image: 'https://images.unsplash.com/photo-1604147706283-d7119b5b822c?auto=format&fit=crop&q=80&w=200&h=150' },
  { kode: 'KL-099-12', nama: 'Susu Kaleng Bendera', jenis: 'Minuman', stok: '100 PCS', info: 'Aman Terkendali', isRed: false, image: 'https://images.unsplash.com/photo-1604147706283-d7119b5b822c?auto=format&fit=crop&q=80&w=200&h=150' },
])

// ==========================================
// 3. FUNGSI BUKA DETAIL MODAL
// ==========================================
const openDetail = (item) => {
  selectedItem.value = item;
  showDetailModal.value = true;
}

// Format Uang Rupiah
const formatRupiah = (value) => {
  return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(value)
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.2s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
</style>