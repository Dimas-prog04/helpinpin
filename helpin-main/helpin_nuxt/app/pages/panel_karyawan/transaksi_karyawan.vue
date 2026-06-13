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
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Kasir & Transaksi</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Kelola keranjang, produk, dan riwayat transaksi</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-8 w-full animate-fade">
        
        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex items-center gap-2 mb-6">
            <div class="w-3 h-3 rounded-full bg-blue-500 shadow-[0_0_10px_rgba(59,130,246,0.5)]"></div>
            <h3 class="text-lg font-bold text-[#19462D]">Riwayat Transaksi Kasir</h3>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-blue-800 bg-blue-50/80 uppercase tracking-widest border-b border-blue-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Tanggal</th>
                  <th class="p-4 font-black">Waktu</th>
                  <th class="p-4 font-black">Quantity</th>
                  <th class="p-4 font-black">Total Transaksi</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in transaksiKasir" :key="i" class="hover:bg-blue-50/30 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.tanggal }}</td>
                  <td class="p-4">
                    <span class="bg-indigo-100 text-indigo-700 px-3 py-1.5 rounded-full text-xs font-bold border border-indigo-200">
                      {{ item.waktu }}
                    </span>
                  </td>
                  <td class="p-4">
                    <span class="bg-yellow-100 text-yellow-700 px-3 py-1.5 rounded-full text-xs font-bold border border-yellow-200">
                      {{ item.qty }}
                    </span>
                  </td>
                  <td class="p-4 font-black text-[#19462D] text-lg">{{ formatRupiah(item.total) }}</td>
                  <td class="p-4">
                    <div class="flex justify-center gap-2">
                      <button class="w-8 h-8 rounded-lg bg-blue-50 text-blue-600 flex items-center justify-center hover:bg-blue-500 hover:text-white transition shadow-sm">
                        <EyeIcon class="w-4 h-4" />
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex items-center gap-2 mb-6">
            <div class="w-3 h-3 rounded-full bg-green-500 shadow-[0_0_10px_rgba(34,197,94,0.5)]"></div>
            <h3 class="text-lg font-bold text-[#19462D]">Keranjang Aktif</h3>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50/80 uppercase tracking-widest border-b border-green-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Nama Produk</th>
                  <th class="p-4 font-black">Harga Satuan</th>
                  <th class="p-4 font-black">Qty</th>
                  <th class="p-4 font-black text-right">Subtotal Item</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Edit</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in keranjang" :key="i" class="hover:bg-green-50/30 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4 font-medium text-gray-500">{{ formatRupiah(item.harga) }}</td>
                  <td class="p-4">
                    <span :class="item.qty > 1 ? 'bg-emerald-100 text-emerald-700 border-emerald-200' : 'bg-orange-100 text-orange-700 border-orange-200'" class="px-3 py-1.5 rounded-full text-xs font-bold border">
                      {{ item.qty }} Pcs
                    </span>
                  </td>
                  <td class="p-4 font-black text-[#19462D] text-right">{{ formatRupiah(item.harga * item.qty) }}</td>
                  <td class="p-4">
                    <div class="flex justify-center gap-2">
                      <button class="w-8 h-8 rounded-lg bg-purple-50 text-purple-600 flex items-center justify-center hover:bg-purple-500 hover:text-white transition shadow-sm">
                        <EditIcon class="w-4 h-4" />
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
              <tfoot class="bg-[#19462D] text-white rounded-b-lg">
                <tr>
                  <td colspan="3" class="p-4 text-right font-medium opacity-90 rounded-bl-lg">Total Pembayaran:</td>
                  <td class="p-4 text-right font-black text-xl">{{ formatRupiah(subtotal) }}</td>
                  <td class="rounded-br-lg"></td>
                </tr>
              </tfoot>
            </table>
          </div>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex items-center gap-2 mb-6">
            <div class="w-3 h-3 rounded-full bg-rose-500 shadow-[0_0_10px_rgba(244,63,94,0.5)]"></div>
            <h3 class="text-lg font-bold text-[#19462D]">Daftar Produk Kasir</h3>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[600px]">
              <thead class="text-xs text-rose-800 bg-rose-50/80 uppercase tracking-widest border-b border-rose-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Nama Produk</th>
                  <th class="p-4 font-black">Status Expired</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in produkKasir" :key="i" class="hover:bg-rose-50/30 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4">
                    <span class="bg-rose-100 text-rose-700 border border-rose-200 px-3 py-1.5 rounded-full text-xs font-bold flex items-center w-max gap-2">
                      <div class="w-2 h-2 rounded-full bg-rose-500 animate-pulse"></div>
                      Exp: {{ item.expired }}
                    </span>
                  </td>
                  <td class="p-4">
                    <div class="flex justify-center gap-2">
                      <button class="w-8 h-8 rounded-lg bg-gray-100 text-gray-600 flex items-center justify-center hover:bg-gray-800 hover:text-white transition shadow-sm">
                        <EditIcon class="w-4 h-4" />
                      </button>
                    </div>
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
import { ref, reactive, computed } from 'vue'
import { MenuIcon, EyeIcon, EditIcon } from 'lucide-vue-next'
import SidebarKaryawan from '~/components/SidebarKaryawan.vue'

const isSidebarOpen = ref(false)

// 1. Data Keranjang
const keranjang = reactive([
  { nama: 'Mie Sedap Goreng', harga: 4000, qty: 2 },
  { nama: 'Selai Kacang', harga: 12000, qty: 1 },
  { nama: 'Pasta Gigi Closeup', harga: 8000, qty: 1 },
])

const subtotal = computed(() => keranjang.reduce((sum, item) => sum + (item.harga * item.qty), 0))

// 2. Data Produk
const produkKasir = ref([
  { nama: 'Mie Sedap Goreng', expired: '05-04-2027' },
  { nama: 'Mie Sedap Goreng', expired: '05-04-2027' },
  { nama: 'Mie Sedap Goreng', expired: '05-04-2027' },
  { nama: 'Mie Sedap Goreng', expired: '05-04-2027' },
  { nama: 'Mie Sedap Goreng', expired: '05-04-2027' },
  { nama: 'Mie Sedap Goreng', expired: '05-04-2027' },
])

// 3. Data Transaksi
const transaksiKasir = ref([
  { qty: '4 Pcs', tanggal: '12-03-2026', waktu: '09.00 AM', total: 24000 },
  { qty: '4 Pcs', tanggal: '12-03-2026', waktu: '09.00 AM', total: 24000 },
  { qty: '4 Pcs', tanggal: '12-03-2026', waktu: '09.00 AM', total: 24000 },
  { qty: '4 Pcs', tanggal: '12-03-2026', waktu: '09.00 AM', total: 24000 },
  { qty: '4 Pcs', tanggal: '12-03-2026', waktu: '09.00 AM', total: 24000 },
  { qty: '4 Pcs', tanggal: '12-03-2026', waktu: '09.00 AM', total: 24000 },
])

// Fungsi untuk Format Rupiah
const formatRupiah = (angka) => {
  return new Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR',
    minimumFractionDigits: 0
  }).format(angka)
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
</style>    