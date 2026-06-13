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
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Riwayat Transaksi</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Pantau semua transaksi masuk dan keluar</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw] animate-fade">
        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2">
              <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Data Transaksi
            </h3>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">ID Transaksi</th>
                  <th class="p-4 font-black">Tanggal</th>
                  <th class="p-4 font-black">Metode</th>
                  <th class="p-4 font-black text-center">Quantity</th>
                  <th class="p-4 font-black">Total Transaksi</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Jenis Transaksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in transaksiData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800 font-mono text-xs">{{ item.id }}</td>
                  <td class="p-4 font-bold text-gray-600">{{ item.tanggal }}</td>
                  <td class="p-4">
                    <span :class="['px-3 py-1 text-[10px] font-black rounded-lg uppercase tracking-wider', item.metode === 'Kasir Offline' ? 'bg-yellow-100 text-yellow-700' : 'bg-blue-100 text-blue-700']">
                      {{ item.metode }}
                    </span>
                  </td>
                  <td class="p-4 font-black text-gray-800 text-center">{{ item.qty }}</td>
                  <td class="p-4 font-black text-green-700">{{ item.total }}</td>
                  <td class="p-4 text-center">
                    <span :class="['px-3 py-1 text-[10px] font-black rounded-lg uppercase tracking-wider', item.jenis === 'Cash' ? 'bg-yellow-100 text-yellow-700' : 'bg-blue-100 text-blue-700']">
                      {{ item.jenis }}
                    </span>
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
import { MenuIcon } from 'lucide-vue-next'
import SidebarAdmin from '~/components/SidebarAdmin.vue'

const isSidebarOpen = ref(false)

const transaksiData = ref([
  { id: 'TX-334-55', tanggal: '12-03-2026', metode: 'Online Market', qty: '4 Pcs', total: 'Rp 24.000,00', jenis: 'E-Wallet' },
  { id: 'TX-334-56', tanggal: '12-03-2026', metode: 'Online Market', qty: '2 Pcs', total: 'Rp 45.000,00', jenis: 'Transfer' },
  { id: 'TX-334-57', tanggal: '12-03-2026', metode: 'Kasir Offline', qty: '10 Pcs', total: 'Rp 120.000,00', jenis: 'Cash' },
  { id: 'TX-334-58', tanggal: '13-03-2026', metode: 'Online Market', qty: '1 Pcs', total: 'Rp 15.000,00', jenis: 'E-Wallet' },
])
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
</style>  