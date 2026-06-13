<template>
  <div class="flex h-screen w-full bg-[#F4FBF7] font-sans overflow-hidden relative">
    <SidebarAdmin :isOpen="isSidebarOpen" @close="isSidebarOpen = false" />

    <main class="flex-1 flex flex-col overflow-y-auto relative w-full no-scrollbar">
      <header class="flex justify-between items-center px-6 md:px-10 py-5 border-b border-gray-200 bg-white/90 backdrop-blur-md shadow-sm z-10 sticky top-0">
        <div class="flex items-center gap-4">
          <button @click="isSidebarOpen = true" class="md:hidden p-2 -ml-2 text-gray-600 hover:bg-gray-100 rounded-lg transition">
            <MenuIcon class="w-6 h-6" />
          </button>
          <div>
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Dashboard Karyawan</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Overview E-commerce, Penjualan, dan AI Monitoring</p>
          </div>
        </div>
        <div class="flex items-center gap-2 bg-blue-50 border border-blue-100 px-4 py-2 rounded-full">
          <div class="w-2 h-2 rounded-full bg-blue-600 animate-pulse"></div>
          <span class="text-xs font-bold text-blue-800 uppercase tracking-widest hidden sm:block">Status Online</span>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 md:gap-8 w-full max-w-[100vw] animate-fade">
        
        <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-14 h-14 rounded-full bg-green-50 flex items-center justify-center text-green-700">
              <BanknoteIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1">Total Omzet Penjualan</p>
              <h3 class="text-2xl font-black text-gray-800">Rp 650.000,00</h3>
            </div>
          </div>
          
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-14 h-14 rounded-full bg-blue-50 flex items-center justify-center text-blue-600">
              <PackageIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1">Koleksi Produk</p>
              <h3 class="text-2xl font-black text-gray-800">6 <span class="text-sm text-gray-500 font-bold">Item</span></h3>
            </div>
          </div>

          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-14 h-14 rounded-full bg-purple-50 flex items-center justify-center text-purple-600">
              <LinkIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1">Blockchain Transaction</p>
              <h3 class="text-2xl font-black text-gray-800">3 <span class="text-sm text-gray-500 font-bold">Block</span></h3>
            </div>
          </div>
        </section>

        <section class="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <div class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6 lg:col-span-2">
            <div class="flex justify-between items-center mb-6">
              <div>
                <h2 class="text-lg font-black text-gray-800">Grafik Omset Penjualan</h2>
                <p class="text-xs font-medium text-gray-400 mt-1">Perbandingan realisasi omset vs target bulanan</p>
              </div>
              <select class="bg-gray-50 border border-gray-200 text-gray-700 text-xs font-bold rounded-lg px-3 py-2 outline-none">
                <option>Tahun 2026</option>
                <option>Tahun 2025</option>
              </select>
            </div>
            <div class="relative w-full h-[300px]">
              <Bar :data="comboChartData" :options="comboChartOptions" />
            </div>
          </div>

          <div class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6 flex flex-col items-center justify-center">
            <h2 class="text-lg font-black text-gray-800 w-full text-left mb-2">Komposisi Penjualan</h2>
            <p class="text-xs font-medium text-gray-400 w-full text-left mb-6">Distribusi produk terjual berdasarkan kategori</p>
            
            <div class="relative h-52 w-52 flex items-center justify-center">
              <Doughnut :data="doughnutChartData" :options="doughnutOptions" />
              <div class="absolute inset-0 flex flex-col items-center justify-center">
                <span class="text-2xl font-black text-gray-800">650k</span>
                <p class="text-[10px] text-gray-400 font-black uppercase tracking-widest">Total</p>
              </div>
            </div>
            
            <div class="w-full grid grid-cols-3 gap-2 mt-6">
              <div class="text-center bg-green-50 rounded-lg p-2">
                <p class="text-[10px] font-black text-green-700 uppercase">Makanan</p>
                <p class="font-bold text-sm text-gray-800">60%</p>
              </div>
              <div class="text-center bg-blue-50 rounded-lg p-2">
                <p class="text-[10px] font-black text-blue-700 uppercase">Minuman</p>
                <p class="font-bold text-sm text-gray-800">25%</p>
              </div>
              <div class="text-center bg-purple-50 rounded-lg p-2">
                <p class="text-[10px] font-black text-purple-700 uppercase">Sembako</p>
                <p class="font-bold text-sm text-gray-800">15%</p>
              </div>
            </div>
          </div>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex items-center gap-2 mb-6">
            <div class="w-2 h-2 rounded-full bg-[#19462D]"></div>
            <h3 class="text-lg font-bold text-[#19462D]">Monitoring Produk AI</h3>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Kode Produk</th>
                  <th class="p-4 font-black">Nama Produk</th>
                  <th class="p-4 font-black">Jenis</th>
                  <th class="p-4 font-black">Stok</th>
                  <th class="p-4 font-black">Informasi AI</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in monitoringData" :key="i" class="hover:bg-gray-50/50 transition-colors group">
                  <td class="p-4 font-bold text-gray-800 font-mono text-[11px]">{{ item.kode }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4 font-medium text-gray-600">{{ item.jenis }}</td>
                  <td class="p-4">
                    <span class="px-2 py-1 rounded-md text-xs font-bold" :class="parseInt(item.stok) < 10 ? 'bg-red-100 text-red-700' : 'bg-gray-100 text-gray-700'">
                      {{ item.stok }}
                    </span>
                  </td>
                  <td class="p-4 font-bold" :class="item.isRed ? 'text-red-500' : 'text-gray-600'">{{ item.info }}</td>
                  <td class="p-4 text-center">
                    <button @click="openModal(item)" class="w-8 h-8 rounded-lg bg-blue-50 text-blue-600 flex items-center justify-center hover:bg-blue-500 hover:text-white mx-auto transition shadow-sm">
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

    <div v-if="isModalOpen" class="fixed inset-0 z-50 flex items-center justify-center p-4 sm:p-6">
      <div class="absolute inset-0 bg-[#0c1a13]/60 backdrop-blur-sm transition-opacity" @click="closeModal"></div>
      
      <div class="bg-white rounded-[32px] shadow-2xl w-full max-w-md overflow-hidden relative z-10 animate-fade">
        <div class="px-6 py-5 border-b border-gray-100 flex justify-between items-center bg-gray-50">
          <h3 class="text-lg font-black text-gray-800 flex items-center gap-2">
            <PackageIcon class="w-5 h-5 text-green-600" />
            Detail Produk
          </h3>
          <button @click="closeModal" class="text-gray-400 hover:text-red-500 hover:bg-red-50 p-2 rounded-full transition-colors">
            <XIcon class="w-5 h-5 block" />
          </button>
        </div>
        
        <div class="p-6 space-y-5" v-if="selectedProduct">
          <div class="flex flex-col gap-1">
            <span class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Kode Produk</span>
            <span class="font-mono text-sm font-bold text-gray-800 bg-gray-100 px-3 py-1.5 rounded-lg w-fit border border-gray-200">{{ selectedProduct.kode }}</span>
          </div>
          
          <div class="grid grid-cols-2 gap-4">
            <div class="flex flex-col gap-1">
              <span class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Nama Produk</span>
              <span class="font-bold text-gray-800">{{ selectedProduct.nama }}</span>
            </div>
            <div class="flex flex-col gap-1">
              <span class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Kategori</span>
              <span class="font-medium text-gray-600">{{ selectedProduct.jenis }}</span>
            </div>
          </div>

          <div class="flex flex-col gap-1 border-t border-gray-100 pt-5">
            <span class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Stok Saat Ini</span>
            <span class="px-3 py-1.5 rounded-lg text-sm font-bold w-fit" :class="parseInt(selectedProduct.stok) < 10 ? 'bg-red-100 text-red-700 border border-red-200' : 'bg-gray-100 text-gray-700 border border-gray-200'">
              {{ selectedProduct.stok }}
            </span>
          </div>

          <div class="flex flex-col gap-2 mt-4 p-5 rounded-2xl border" :class="selectedProduct.isRed ? 'bg-red-50 border-red-100' : 'bg-green-50 border-green-100'">
            <span class="text-[10px] font-black uppercase tracking-widest flex items-center gap-1.5" :class="selectedProduct.isRed ? 'text-red-800' : 'text-green-800'">
              <span class="w-2 h-2 rounded-full animate-pulse" :class="selectedProduct.isRed ? 'bg-red-500' : 'bg-green-500'"></span>
              Notifikasi AI
            </span>
            <span class="font-black text-sm" :class="selectedProduct.isRed ? 'text-red-600' : 'text-green-600'">
              {{ selectedProduct.info }}
            </span>
          </div>
        </div>

        <div class="px-6 py-4 border-t border-gray-100 bg-gray-50 flex justify-end">
          <button @click="closeModal" class="px-6 py-2.5 rounded-xl bg-gray-800 text-white text-sm font-bold hover:bg-gray-700 active:scale-95 transition-all shadow-md">
            Selesai
          </button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'
// Menambahkan XIcon untuk tombol tutup modal
import { MenuIcon, BanknoteIcon, PackageIcon, LinkIcon, EyeIcon, XIcon } from 'lucide-vue-next'
import SidebarKaryawan from '~/components/SidebarKaryawan.vue'

// Import Chart.js dan Vue-ChartJS
import { Bar, Doughnut } from 'vue-chartjs'
import { 
  Chart as ChartJS, Title, Tooltip, Legend, BarElement, LineElement, PointElement,
  CategoryScale, LinearScale, ArcElement, LineController 
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, LineElement, PointElement, CategoryScale, LinearScale, ArcElement, LineController)

const isSidebarOpen = ref(false)

// Data Table (TETAP SAMA)
const monitoringData = ref([
  { kode: 'TX-076-01', nama: 'Mie Sedap Goreng', jenis: 'Makanan', stok: '30 PCS', info: '3 Hari Lagi Expired', isRed: true },
  { kode: 'DD-987-37', nama: 'Pasta Gigi Closeup', jenis: 'Cosmetic', stok: '5 PCS', info: 'Stok Menipis', isRed: true },
  { kode: 'TY-055-45', nama: 'Selai Kacang', jenis: 'Makanan', stok: '7 PCS', info: '7 Hari Lagi Expired', isRed: true },
  { kode: 'KI-547-12', nama: 'Tepung Segitiga', jenis: 'Makanan', stok: '3 PCS', info: 'Stok Menipis', isRed: true },
])

// ===== FUNGSI UNTUK MODAL =====
const isModalOpen = ref(false)
const selectedProduct = ref(null)

const openModal = (item) => {
  selectedProduct.value = item
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
  // Menghapus data setelah animasi fade selesai agar tidak berkedip
  setTimeout(() => {
    selectedProduct.value = null
  }, 300)
}
// ==============================

// Konfigurasi Combo Chart (Omzet vs Target)
const comboChartData = ref({
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun', 'Jul', 'Agt', 'Sep', 'Okt', 'Nov', 'Des'],
  datasets: [
    {
      type: 'line',
      label: 'Target Omset (Juta Rp)',
      data: [450, 600, 650, 800, 550, 650, 600, 750, 650, 550, 680, 600],
      borderColor: '#94a3b8',
      borderWidth: 2,
      borderDash: [5, 5],
      tension: 0.4,
      pointRadius: 0
    },
    {
      type: 'bar',
      label: 'Realisasi Penjualan (Juta Rp)',
      backgroundColor: '#19462D', // Warna Hijau Gelap Karyawan
      borderRadius: 6,
      maxBarThickness: 24,
      data: [500, 620, 680, 750, 580, 680, 620, 700, 620, 500, 700, 620]
    }
  ]
})

const comboChartOptions = {
  responsive: true, maintainAspectRatio: false,
  interaction: { mode: 'index', intersect: false },
  plugins: { 
    legend: { position: 'top', align: 'end', labels: { usePointStyle: true, boxWidth: 8, font: { weight: 'bold', size: 11 } } }
  },
  scales: {
    y: { grid: { color: '#f1f5f9', drawBorder: false }, ticks: { font: { weight: 'bold' } } },
    x: { grid: { display: false, drawBorder: false }, ticks: { font: { weight: 'bold', color: '#64748b' } } }
  }
}

// Konfigurasi Doughnut Chart (Komposisi Penjualan)
const doughnutChartData = ref({
  labels: ['Makanan', 'Minuman', 'Sembako'],
  datasets: [{
    data: [60, 25, 15],
    backgroundColor: ['#16a34a', '#3b82f6', '#a855f7'],
    borderWidth: 0,
    hoverOffset: 4
  }]
})

const doughnutOptions = {
  responsive: true, maintainAspectRatio: false, cutout: '75%',
  plugins: { legend: { display: false }, tooltip: { padding: 12, cornerRadius: 8 } }
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.3s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px) scale(0.98); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}
</style>