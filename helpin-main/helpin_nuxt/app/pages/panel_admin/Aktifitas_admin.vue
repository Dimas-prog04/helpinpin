<template>
  <div class="flex h-screen w-full bg-[#F4FBF7] font-sans overflow-hidden relative">
    
    <SidebarAdmin :isOpen="isSidebarOpen" @close="isSidebarOpen = false" />

    <main class="flex-1 flex flex-col overflow-y-auto relative w-full no-scrollbar">
      <header class="flex justify-between items-center px-6 md:px-10 py-5 border-b border-gray-200 bg-white/90 backdrop-blur-md shadow-sm z-10 sticky top-0">
        <div class="flex items-center gap-4">
          <button @click="isSidebarOpen = true" class="md:hidden p-2 -ml-2 text-gray-600 hover:bg-gray-100 rounded-lg transition">
            <MenuIcon class="w-6 h-6" />
          </button>
          <div class="flex items-center gap-3">
            <div class="w-10 h-10 rounded-xl bg-blue-50 flex items-center justify-center text-blue-600 hidden sm:flex">
              <ActivityIcon class="w-5 h-5" />
            </div>
            <div>
              <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Log Aktifitas</h1>
              <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Pantau jejak aktivitas dan status sistem</p>
            </div>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw] animate-fade">
        
        <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mb-2">
          <div v-for="i in 3" :key="i" class="bg-white p-4 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 hover:shadow-md transition">
            <div class="w-12 h-12 rounded-xl bg-green-50 flex items-center justify-center text-green-600 shrink-0">
              <UserCheckIcon class="w-6 h-6" />
            </div>
            <div>
              <h4 class="text-sm font-black text-gray-800 mb-1 leading-tight">Dashboard Karyawan {{ i }}</h4>
              <p class="text-[10px] font-bold text-gray-500 flex items-center gap-1.5 uppercase tracking-wider">
                <span class="w-2 h-2 rounded-full bg-blue-500 animate-pulse"></span> <strong class="text-blue-700">Online</strong>
              </p>
            </div>
          </div>
          
          <div class="bg-white p-4 rounded-2xl border border-gray-100 shadow-sm flex items-center gap-4 opacity-75">
            <div class="w-12 h-12 rounded-xl bg-red-50 flex items-center justify-center text-red-500 shrink-0">
              <UserXIcon class="w-6 h-6" />
            </div>
            <div>
              <h4 class="text-sm font-black text-gray-600 mb-1 leading-tight">Dashboard Karyawan 4</h4>
              <p class="text-[10px] font-bold text-gray-500 flex items-center gap-1.5 uppercase tracking-wider">
                <span class="w-2 h-2 rounded-full bg-red-500"></span> <span class="px-1.5 py-0.5 bg-red-100 text-red-600 rounded text-[9px]">Offline</span>
              </p>
            </div>
          </div>
        </section>

        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mb-6">
            <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2">
              <ClipboardListIcon class="w-5 h-5 text-[#19462D]" /> Rekam Aktifitas
            </h3>
            
            <div class="flex flex-col sm:flex-row gap-3 w-full sm:w-auto">
              <div class="relative w-full sm:w-64">
                <SearchIcon class="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-gray-400" />
                <input type="text" placeholder="Cari Hash Aktifitas..." class="w-full pl-9 pr-4 py-2.5 border border-gray-200 rounded-xl outline-none focus:border-[#19462D] bg-gray-50 text-sm">
              </div>
            </div>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Judul Aktifitas</th>
                  <th class="p-4 font-black">Hash Sistem</th>
                  <th class="p-4 font-black">Tanggal & Waktu</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in aktifitasData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.judul }}</td>
                  <td class="p-4">
                    <div class="flex items-center gap-2 bg-gray-100 px-3 py-1.5 rounded-lg w-fit border border-gray-200">
                      <FingerprintIcon class="w-4 h-4 text-gray-500" />
                      <span class="text-[11px] font-mono font-bold text-gray-600">{{ item.hash }}</span>
                    </div>
                  </td>
                  <td class="p-4">
                    <p class="font-bold text-gray-700">{{ item.tanggal }}</p>
                    <p class="text-xs text-gray-500 font-medium">{{ item.waktu }}</p>
                  </td>
                  <td class="p-4 text-center">
                    <button @click="showAktifitasModal = true" class="w-8 h-8 rounded-lg bg-blue-50 text-blue-600 flex items-center justify-center hover:bg-blue-500 hover:text-white mx-auto transition shadow-sm border border-blue-100">
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

    <div v-if="showAktifitasModal" class="fixed inset-0 z-[100] flex items-center justify-center bg-black/40 backdrop-blur-sm p-4 overflow-y-auto" @click.self="showAktifitasModal = false">
      <div class="bg-white rounded-[32px] w-full max-w-5xl my-auto shadow-2xl relative animate-fade flex flex-col max-h-[90vh]">
        
        <div class="flex justify-between items-center p-6 md:p-8 border-b border-gray-100 sticky top-0 bg-white rounded-t-[32px] z-10">
          <div class="flex items-center gap-4">
            <button @click="showAktifitasModal = false" class="p-2 hover:bg-gray-100 rounded-xl transition flex items-center justify-center">
              <XIcon class="w-8 h-8 text-black" />
            </button>
            <h1 class="text-2xl md:text-3xl font-extrabold text-[#19462D]">Detail Log Aktifitas</h1>
          </div>
        </div>
        
        <div class="p-6 md:p-8 overflow-y-auto flex flex-col gap-6 no-scrollbar">
          
          <div class="border border-gray-100 bg-[#F8FAFC] rounded-[24px] p-6 md:p-8">
            <div class="flex items-center gap-3 mb-6 text-xs font-extrabold tracking-widest text-[#19462D] uppercase">
              <span class="w-2.5 h-2.5 rounded-full bg-[#19462D]"></span> 
              INFORMASI UTAMA
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Judul Aktifitas</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">Penghapusan Tabel</div>
              </div>
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Tabel Terdampak</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">Tabel_Produk</div>
              </div>
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Nama Pengguna</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">Giska678</div>
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
              <div class="md:col-span-2">
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Deskripsi Lengkap</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800 min-h-[80px]">
                  Tabel data produk TX-678-01 Telah di hapus
                </div>
              </div>
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Tipe Aksi</label>
                <div class="inline-flex px-4 py-2 mt-1 rounded-md border border-orange-200 bg-orange-50 text-orange-500 font-extrabold text-xs tracking-wider uppercase">
                  UPDATED
                </div>
              </div>
            </div>
          </div>

          <div class="border border-gray-100 bg-[#F8FAFC] rounded-[24px] p-6 md:p-8">
            <div class="flex items-center gap-3 mb-6 text-xs font-extrabold tracking-widest text-[#19462D] uppercase">
              <span class="w-2.5 h-2.5 rounded-full bg-[#19462D]"></span> 
              JEJAK KREDENSIAL & LOKASI
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">IP Address</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">192.168.1.104</div>
              </div>
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Kordinat Lokasi (LAT)</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">-6.200000</div>
              </div>
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Kordinat Lokasi (LONG)</label>
                <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">106.816666</div>
              </div>
            </div>

            <div>
              <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">User Agent (Browser/OS)</label>
              <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800">
                CHROME, 2342343, LINUX
              </div>
            </div>
          </div>

          <div class="border border-gray-100 bg-[#F8FAFC] rounded-[24px] p-6 md:p-8">
            <div class="flex items-center gap-3 mb-6 text-xs font-extrabold tracking-widest text-[#19462D] uppercase">
              <span class="w-2.5 h-2.5 rounded-full bg-[#19462D]"></span> 
              PERBANDINGAN DATA (JSON)
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 bg-white p-6 rounded-2xl border border-gray-100">
              
              <div>
                <h4 class="text-xs font-extrabold text-gray-600 mb-4">Data Lama (Old)</h4>
                <div class="border border-gray-100 rounded-xl overflow-hidden">
                  <table class="w-full text-xs text-left">
                    <thead class="bg-gray-50 text-gray-500 font-bold border-b border-gray-100">
                      <tr>
                        <th class="p-3 w-1/3 border-r border-gray-100">Key</th>
                        <th class="p-3">Value</th>
                      </tr>
                    </thead>
                    <tbody class="text-gray-700 font-mono">
                      <tr class="border-b border-gray-100">
                        <td class="p-3 border-r border-gray-100">status</td>
                        <td class="p-3">Pending</td>
                      </tr>
                      <tr>
                        <td class="p-3 border-r border-gray-100">updated_at</td>
                        <td class="p-3">2026-05-23T03:31:45.000000Z</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>

              <div>
                <h4 class="text-xs font-extrabold text-gray-600 mb-4">Data Baru (New)</h4>
                <div class="border border-gray-100 rounded-xl overflow-hidden">
                  <table class="w-full text-xs text-left">
                    <thead class="bg-gray-50 text-gray-500 font-bold border-b border-gray-100">
                      <tr>
                        <th class="p-3 w-1/3 border-r border-gray-100">Key</th>
                        <th class="p-3">Value</th>
                      </tr>
                    </thead>
                    <tbody class="text-gray-700 font-mono">
                      <tr class="border-b border-gray-100">
                        <td class="p-3 border-r border-gray-100">status</td>
                        <td class="p-3">Diterima</td>
                      </tr>
                      <tr>
                        <td class="p-3 border-r border-gray-100">updated_at</td>
                        <td class="p-3">2026-05-23 06:12:33</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>

            </div>
          </div>

          <div class="border border-gray-100 bg-[#F8FAFC] rounded-[24px] p-6 md:p-8">
            <div class="flex items-center gap-3 mb-6 text-xs font-extrabold tracking-widest text-[#19462D] uppercase">
              <span class="w-2.5 h-2.5 rounded-full bg-[#19462D]"></span> 
              INTEGRITAS KEAMANAN
            </div>
            <div>
              <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Transaction Hash</label>
              <div class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm font-bold text-gray-800 break-all font-mono">
                768789654I83YFHVB J6R5E
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import { MenuIcon, SearchIcon, EyeIcon, UserCheckIcon, UserXIcon, XIcon, ActivityIcon, FingerprintIcon, ClipboardListIcon } from 'lucide-vue-next'
import SidebarAdmin from '~/components/SidebarAdmin.vue'

const isSidebarOpen = ref(false)
const showAktifitasModal = ref(false)

// Data tiruan untuk tabel depan
const aktifitasData = ref([
  { judul: 'Penghapusan Tabel', hash: '768789654I83YFHVB', tanggal: '12-04-2026', waktu: '09.00 AM' },
  { judul: 'Penambahan Stok Barang', hash: '9982736KJHDGFYURT', tanggal: '12-04-2026', waktu: '10.30 AM' },
  { judul: 'Penghapusan Karyawan', hash: 'AA23847ZXCVBNM876', tanggal: '13-04-2026', waktu: '08.15 AM' },
  { judul: 'Update Harga Jual', hash: '1209843LKJHGFRTYU', tanggal: '13-04-2026', waktu: '14.20 PM' },
])
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.3s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
</style>