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
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Buku Kas</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Monitoring sirkulasi keuangan</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw] animate-fade">
        
        <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4">
            <div class="w-14 h-14 rounded-xl bg-green-50 flex items-center justify-center text-green-700">
              <WalletIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1">Total Kas</p>
              <h3 class="text-2xl font-black text-gray-800">Rp 650.000,00</h3>
            </div>
          </div>
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4">
            <div class="w-14 h-14 rounded-xl bg-blue-50 flex items-center justify-center text-blue-600">
              <TrendingUpIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1">Uang Masuk</p>
              <h3 class="text-2xl font-black text-gray-800">Rp 700.000,00</h3>
            </div>
          </div>
          <div class="bg-white p-6 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4">
            <div class="w-14 h-14 rounded-xl bg-red-50 flex items-center justify-center text-red-500">
              <TrendingDownIcon class="w-7 h-7" />
            </div>
            <div>
              <p class="text-[11px] font-bold text-gray-400 uppercase tracking-wider mb-1">Uang Keluar</p>
              <h3 class="text-2xl font-black text-gray-800">Rp 50.000,00</h3>
            </div>
          </div>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2 mb-6">
            <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Catatan Kas
          </h3>
          
          <div class="flex flex-col md:flex-row gap-4 mb-6">
            <input type="text" placeholder="Keterangan Transaksi..." class="w-full md:flex-[2] p-3 border border-gray-200 rounded-xl outline-none focus:border-[#19462D] bg-gray-50 text-sm">
            <select class="w-full md:flex-[1] p-3 border border-gray-200 rounded-xl outline-none focus:border-[#19462D] bg-gray-50 text-sm font-semibold text-gray-600">
              <option>Kategori</option>
              <option>Uang Masuk</option>
              <option>Uang Keluar</option>
            </select>
            <input type="text" placeholder="Nominal..." class="w-full md:flex-[1] p-3 border border-gray-200 rounded-xl outline-none focus:border-[#19462D] bg-gray-50 text-sm">
            <button class="bg-blue-500 hover:bg-blue-600 text-white px-6 py-3 rounded-xl font-bold text-sm flex items-center justify-center gap-2 transition whitespace-nowrap shadow-sm">
              <SaveIcon class="w-4 h-4" /> Catat Kas
            </button>
          </div>

          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Keterangan Transaksi</th>
                  <th class="p-4 font-black">Jenis</th>
                  <th class="p-4 font-black">Nominal</th>
                  <th class="p-4 font-black rounded-tr-lg">Waktu Pencatatan</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in kasData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.ket }}</td>
                  <td class="p-4">
                    <span :class="['px-3 py-1.5 text-[11px] font-black rounded-lg uppercase tracking-wider', item.jenis === 'Masuk' ? 'bg-blue-500 text-white' : 'bg-red-500 text-white']">
                      {{ item.jenis }}
                    </span>
                  </td>
                  <td class="p-4 font-black text-gray-800">{{ item.nominal }}</td>
                  <td class="p-4 font-bold text-gray-500">{{ item.waktu }}</td>
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
import { MenuIcon, WalletIcon, TrendingUpIcon, TrendingDownIcon, SaveIcon } from 'lucide-vue-next'
import SidebarKaryawan from '~/components/SidebarKaryawan.vue'

const isSidebarOpen = ref(false)

const kasData = ref([
  { ket: 'Saldo modal awal Usaha', jenis: 'Masuk', nominal: 'Rp 700.000,00', waktu: '12-04-2026' },
  { ket: 'Pembelian Bahan Baku', jenis: 'Keluar', nominal: 'Rp 150.000,00', waktu: '12-04-2026' },
  { ket: 'Penjualan Produk X', jenis: 'Masuk', nominal: 'Rp 300.000,00', waktu: '12-04-2026' },
  { ket: 'Beli Keperluan Operasional', jenis: 'Keluar', nominal: 'Rp 50.000,00', waktu: '13-04-2026' },
])
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
</style>