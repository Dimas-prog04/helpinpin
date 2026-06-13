<template>
  <div class="flex h-screen w-full bg-[#F4FBF7] font-sans overflow-hidden relative">
    
    <SidebarHybrid :isOpen="isSidebarOpen" @close="isSidebarOpen = false" />

    <main class="flex-1 flex flex-col overflow-y-auto relative w-full no-scrollbar">
      
      <header class="flex justify-between items-center px-6 md:px-10 py-5 border-b border-gray-200 bg-white/90 backdrop-blur-md shadow-sm z-10 sticky top-0">
        <div class="flex items-center gap-4">
          <button @click="isSidebarOpen = true" class="md:hidden p-2 -ml-2 text-gray-600 hover:bg-gray-100 rounded-lg transition">
            <MenuIcon class="w-6 h-6" />
          </button>
          <div>
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Dashboard</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Dashboard dari sistem</p>
          </div>
        </div>
        <div class="flex items-center gap-2 bg-blue-50 px-4 py-2 rounded-full border border-blue-100">
          <div class="w-2 h-2 rounded-full bg-blue-600 animate-pulse"></div>
          <span class="text-xs font-bold text-blue-800 uppercase">Status Online</span>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw] animate-fade">
        
        <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-14 h-14 rounded-xl bg-green-50 flex items-center justify-center text-green-700">
              <BanknoteIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-500 uppercase tracking-wider mb-1">Total Omzet Penjualan</p>
              <h3 class="text-2xl font-black text-gray-800">Rp 650.000,00</h3>
            </div>
          </div>
          
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-14 h-14 rounded-xl bg-green-50 flex items-center justify-center text-green-700">
              <PackageIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-500 uppercase tracking-wider mb-1">Koleksi Produk</p>
              <h3 class="text-2xl font-black text-gray-800">6 Item</h3>
            </div>
          </div>

          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-14 h-14 rounded-xl bg-green-50 flex items-center justify-center text-green-700">
              <LinkIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-500 uppercase tracking-wider mb-1">Blockchain Transaction</p>
              <h3 class="text-2xl font-black text-gray-800">3 Block</h3>
            </div>
          </div>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2">
              <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Grafik Omset
            </h3>
            <select class="bg-gray-50 border border-gray-200 text-gray-700 text-xs font-bold rounded-lg px-3 py-2 outline-none">
              <option>Bulanan</option>
            </select>
          </div>
          <div class="relative w-full h-[300px]">
             <Bar :data="chartData" :options="chartOptions" />
          </div>
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
                  <td class="p-4 font-bold text-gray-800">{{ item.kode }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4 font-medium text-gray-600">{{ item.jenis }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.stok }}</td>
                  <td class="p-4 font-bold" :class="item.isRed ? 'text-red-500' : 'text-gray-600'">{{ item.info }}</td>
                  <td class="p-4 text-center">
                    <button class="w-8 h-8 rounded-lg bg-blue-500 text-white flex items-center justify-center hover:bg-blue-600 mx-auto transition">
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
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { MenuIcon, BanknoteIcon, PackageIcon, LinkIcon, EyeIcon } from 'lucide-vue-next'
import SidebarHybrid from '~/components/SidebarHybrid.vue'
import { Bar } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const isSidebarOpen = ref(false)

// Data Table
const monitoringData = ref([
  { kode: 'TX-076-01', nama: 'Mie Sedap Goreng', jenis: 'Makanan', stok: '30 PCS', info: '3 Hari Lagi Expired', isRed: true },
  { kode: 'DD-987-37', nama: 'Pasta Gigi Closeup', jenis: 'Cosmetic', stok: '5 PCS', info: 'Stok Menipis', isRed: true },
  { kode: 'TY-055-45', nama: 'Selai Kacang', jenis: 'Makanan', stok: '7 PCS', info: '7 Hari Lagi Expired', isRed: true },
])

// Data Chart (Menggunakan standar ChartJS Nuxt)
const chartData = ref({
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun', 'Jul', 'Agt', 'Sep', 'Okt', 'Nov', 'Des'],
  datasets: [{
    label: 'Omset (Ratusan Ribu)',
    backgroundColor: '#19462D',
    borderRadius: 6,
    data: [50, 60, 68, 75, 58, 68, 62, 70, 62, 50, 70, 62]
  }]
})

const chartOptions = {
  responsive: true, maintainAspectRatio: false,
  plugins: { legend: { display: false } },
  scales: {
    y: { beginAtZero: true, grid: { color: '#f1f5f9' } },
    x: { grid: { display: false } }
  }
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

.animate-fade { animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>