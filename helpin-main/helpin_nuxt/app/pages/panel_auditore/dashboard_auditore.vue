<script setup lang="ts">
import { ref } from 'vue'
import { SearchIcon, EyeIcon, XIcon, MenuIcon } from 'lucide-vue-next'
// Panggil komponen Sidebar kamu
import SidebarAuditore from '~/components/SidebarAuditore.vue'

// State untuk Sidebar Mobile
const isSidebarOpen = ref(false)

// State untuk Modal
const isModalOpen = ref(false)
const selectedData = ref<any>(null)

// Data Dummy Tabel Audit
const auditData = ref([
  { id: 1, no: 1, executor: 'M. Rizal', tujuan: 'Tabel Transaksi', status: 'Approved', ket_awal: 'bahlil mengubah tabel transaksi pada id 231', detail: 'Old Data', rawStatus: 'Approved' },
  { id: 2, no: 1, executor: 'M. Rizal', tujuan: 'Tabel Transaksi', status: 'Approved', ket_awal: 'bahlil mengubah tabel transaksi pada id 231', detail: 'Old Data', rawStatus: 'Approved' },
  { id: 3, no: 1, executor: 'M. Rizal', tujuan: 'Tabel Transaksi', status: 'On Review', ket_awal: 'Tabel data produk TX-678-01 Telah di hapus', detail: 'Old Data', rawStatus: 'On Review' },
  { id: 4, no: 1, executor: 'M. Rizal', tujuan: 'Tabel Transaksi', status: 'Approved', ket_awal: 'bahlil mengubah tabel transaksi pada id 231', detail: 'Old Data', rawStatus: 'Approved' },
  { id: 5, no: 1, executor: 'M. Rizal', tujuan: 'Tabel Transaksi', status: 'Approved', ket_awal: 'bahlil mengubah tabel transaksi pada id 231', detail: 'Old Data', rawStatus: 'Approved' },
])

// Fungsi buka modal dan kirim data spesifik baris yang diklik
const openModal = (data: any) => {
  selectedData.value = data
  isModalOpen.value = true
}

// Fungsi tutup modal
const closeModal = () => {
  isModalOpen.value = false
  setTimeout(() => {
    selectedData.value = null
  }, 300) 
}

// Fungsi dummy untuk tombol Approve & Reject
const handleAction = (action: string) => {
  console.log(`Data ID ${selectedData.value.id} di-${action}`);
  // Di sini nanti kamu bisa tembak API ke Backend
  closeModal();
}
</script>

<template>
  <div class="flex h-screen w-full bg-[#f4fbf7] font-sans overflow-hidden">
    
    <!-- SIDEBAR -->
    <SidebarAuditore :isOpen="isSidebarOpen" @close="isSidebarOpen = false" />

    <!-- MAIN CONTENT KANAN -->
    <main class="flex-1 flex flex-col h-screen overflow-hidden relative z-10 w-full">
      
      <!-- HEADER ATAS -->
      <header class="bg-white/80 backdrop-blur-md px-6 md:px-8 py-5 flex items-center justify-between border-b border-gray-200/50 shadow-sm shrink-0">
        <div class="flex items-center gap-4">
          <button @click="isSidebarOpen = true" class="md:hidden text-[#0a3f12] p-2 hover:bg-green-50 rounded-lg transition">
            <MenuIcon class="w-6 h-6" />
          </button>
          
          <div>
            <h1 class="text-[#0a3f12] text-xl md:text-2xl font-extrabold mb-0.5">Dashboard</h1>
            <p class="text-gray-500 text-xs font-medium">Dashboard dari sistem</p>
          </div>
        </div>
        
        <!-- Status Badge -->
        <div class="bg-[#dcfce7] text-[#166534] px-3 py-1.5 md:px-4 md:py-2 rounded-lg flex items-center gap-2 border border-green-200">
          <div class="w-2 h-2 bg-green-600 rounded-full animate-pulse"></div>
          <div class="hidden md:block text-left">
            <p class="text-[9px] font-bold leading-tight">STATUS</p>
            <p class="text-xs font-black leading-tight tracking-wide">ONLINE</p>
          </div>
          <p class="md:hidden text-xs font-black tracking-wide">ONLINE</p>
        </div>
      </header>

      <!-- KONTEN DASHBOARD (SCROLLABLE) -->
      <div class="flex-1 overflow-y-auto p-4 md:p-8">
        
        <!-- WRAPPER TABEL -->
        <div class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
          
          <!-- Card Header & Search -->
          <div class="p-5 md:p-6 flex flex-col md:flex-row md:items-center justify-between gap-4 border-b border-gray-100">
            <div>
              <div class="flex items-center gap-2 mb-1">
                <div class="w-1.5 h-1.5 bg-gray-400 rounded-full"></div>
                <h2 class="text-[#0a3f12] text-lg font-extrabold">Tabel Data Audit</h2>
              </div>
              <p class="text-gray-400 text-xs font-medium pl-3.5">Lacak jejak siklus penerimaan data</p>
            </div>
            
            <!-- Input Cari -->
            <div class="relative w-full md:w-72 group">
              <div class="absolute inset-y-0 left-0 pl-3.5 flex items-center pointer-events-none">
                <SearchIcon class="w-4 h-4 text-gray-400 group-focus-within:text-green-600 transition-colors" />
              </div>
              <input 
                type="text" 
                placeholder="Cari Data..." 
                class="block w-full pl-10 pr-4 py-2 border border-gray-200 rounded-lg bg-gray-50 text-sm focus:outline-none focus:ring-1 focus:ring-green-500 focus:border-green-500 transition-all"
              >
            </div>
          </div>

          <!-- TABLE CONTAINER -->
          <div class="overflow-x-auto p-4 md:p-6">
            <table class="w-full text-center text-sm min-w-[800px]">
              
              <!-- Table Head -->
              <thead>
                <tr class="bg-[#dcfce7] text-[#166534]">
                  <th class="py-3 px-4 font-bold text-xs rounded-l-md">NO</th>
                  <th class="py-3 px-4 font-bold text-xs">EXECUTOR</th>
                  <th class="py-3 px-4 font-bold text-xs">TUJUAN DATA</th>
                  <th class="py-3 px-4 font-bold text-xs">STATUS DATA</th>
                  <th class="py-3 px-4 font-bold text-xs w-64">KETERANGAN</th>
                  <th class="py-3 px-4 font-bold text-xs">DETAIL DATA</th>
                  <th class="py-3 px-4 font-bold text-xs rounded-r-md">AKSI</th>
                </tr>
              </thead>
              
              <!-- Table Body -->
              <tbody class="text-gray-700 font-medium">
                <tr v-for="(row, index) in auditData" :key="index" class="border-b border-gray-100 hover:bg-gray-50 transition-colors">
                  <td class="py-4 px-4 font-bold text-gray-900">{{ row.no }}</td>
                  <td class="py-4 px-4">{{ row.executor }}</td>
                  <td class="py-4 px-4 font-bold text-gray-900">{{ row.tujuan }}</td>
                  
                  <!-- Status Data Badge -->
                  <td class="py-4 px-4">
                    <span :class="[
                      'inline-block px-3 py-1 text-xs font-bold rounded border bg-white',
                      row.rawStatus === 'Approved' ? 'text-[#22c55e] border-[#22c55e]' : 'text-[#f59e0b] border-[#f59e0b]'
                    ]">
                      {{ row.status }}
                    </span>
                  </td>
                  
                  <!-- Keterangan -->
                  <td class="py-4 px-4 text-xs text-gray-500 leading-relaxed text-center" v-html="row.ket_awal.replace('id 231', '<strong class=\'text-gray-900\'>id 231</strong>')">
                  </td>
                  
                  <!-- Detail Data Badge -->
                  <td class="py-4 px-4">
                    <span class="inline-block px-3 py-1 text-xs font-bold text-[#f59e0b] border border-[#f59e0b] rounded bg-white">
                      {{ row.detail }}
                    </span>
                  </td>
                  
                  <!-- Aksi Button -->
                  <td class="py-4 px-4">
                    <button 
                      @click="openModal(row)"
                      class="bg-[#1877f2] hover:bg-blue-600 text-white p-1.5 rounded transition-all focus:outline-none"
                    >
                      <EyeIcon class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          
        </div>
      </div>
    </main>

    <!-- ========================================== -->
    <!-- MODAL POPUP (DETAIL AUDIT) -->
    <!-- ========================================== -->
    <Teleport to="body">
      <div v-if="isModalOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4">
        <!-- Backdrop -->
        <div class="absolute inset-0 bg-black/50 backdrop-blur-sm transition-opacity" @click="closeModal"></div>
        
        <!-- Modal Content Box -->
        <div class="bg-white rounded-[20px] shadow-2xl w-full max-w-4xl relative z-10 overflow-hidden transform transition-all flex flex-col max-h-[90vh]">
          
          <!-- Modal Header -->
          <div class="px-8 py-6 flex items-center gap-5">
            <button @click="closeModal" class="text-black hover:text-red-500 transition-colors">
              <XIcon class="w-8 h-8 stroke-[2.5]" />
            </button>
            <h2 class="text-[22px] font-extrabold text-[#0a3f12]">Detail Tabel Data Audit</h2>
          </div>

          <!-- Modal Body -->
          <div class="px-8 pb-8 overflow-y-auto flex-1">
            
            <!-- Box Content Inner (Light gray background) -->
            <div class="bg-[#f8f9fa] border border-gray-200 rounded-[16px] p-8">
              
              <!-- Title Inside -->
              <div class="flex items-center gap-2 mb-6">
                <div class="w-1.5 h-1.5 bg-[#0a3f12] rounded-full"></div>
                <h3 class="text-[#0a3f12] text-[11px] font-black tracking-widest uppercase">Informasi Utama</h3>
              </div>

              <!-- Grid Fields (2 Columns) -->
              <div class="grid grid-cols-1 md:grid-cols-2 gap-x-12 gap-y-6">
                
                <!-- Kolom Kiri -->
                <div class="space-y-6">
                  <!-- Field: Executor -->
                  <div>
                    <label class="block text-[13px] font-extrabold text-[#0a3f12] mb-2 uppercase tracking-wide">Executor</label>
                    <div class="w-full px-4 py-3 bg-white border border-gray-200 rounded-[8px] text-[14px] font-bold text-gray-800">
                      {{ selectedData?.executor }}
                    </div>
                  </div>
                  <!-- Field: Keterangan (Taller) -->
                  <div>
                    <label class="block text-[13px] font-extrabold text-[#0a3f12] mb-2 uppercase tracking-wide">Keterangan</label>
                    <div class="w-full px-4 py-3 bg-white border border-gray-200 rounded-[8px] text-[14px] font-bold text-gray-800 min-h-[130px] flex items-start">
                      {{ selectedData?.ket_awal }}
                    </div>
                  </div>
                </div>

                <!-- Kolom Kanan -->
                <div class="space-y-6">
                  <!-- Field: Tujuan Data -->
                  <div>
                    <label class="block text-[13px] font-extrabold text-[#0a3f12] mb-2 uppercase tracking-wide">Tujuan Data</label>
                    <div class="w-full px-4 py-3 bg-white border border-gray-200 rounded-[8px] text-[14px] font-bold text-gray-800">
                      {{ selectedData?.tujuan }}
                    </div>
                  </div>
                  <!-- Field: Status Data (Badge) -->
                  <div>
                    <label class="block text-[13px] font-extrabold text-[#0a3f12] mb-2 uppercase tracking-wide">Status Data</label>
                    <div class="pt-1">
                      <span :class="[
                        'inline-block px-4 py-1.5 text-[13px] font-bold rounded-[6px] border bg-white',
                        selectedData?.rawStatus === 'Approved' ? 'text-[#22c55e] border-[#22c55e]' : 'text-[#f59e0b] border-[#f59e0b]'
                      ]">
                        {{ selectedData?.status }}
                      </span>
                    </div>
                  </div>
                  <!-- Field: Detail Data (Badge) -->
                  <div>
                    <label class="block text-[13px] font-extrabold text-[#0a3f12] mb-2 uppercase tracking-wide">Detail Data</label>
                    <div class="pt-1">
                      <span class="inline-block px-4 py-1.5 text-[13px] font-bold text-[#f59e0b] border border-[#f59e0b] rounded-[6px] bg-white">
                        {{ selectedData?.detail }}
                      </span>
                    </div>
                  </div>
                </div>
                
              </div>
            </div>

            <!-- Action Buttons (HANYA MUNCUL JIKA STATUS 'On Review') -->
            <div class="mt-6 flex items-center justify-end gap-4" v-if="selectedData?.rawStatus === 'On Review'">
              <button 
                @click="handleAction('Reject')" 
                class="px-10 py-3 bg-[#ff4d4f] hover:bg-red-500 text-white text-[15px] font-extrabold rounded-[8px] transition-all focus:outline-none"
              >
                Reject
              </button>
              <button 
                @click="handleAction('Approve')" 
                class="px-10 py-3 bg-[#2bc155] hover:bg-green-500 text-white text-[15px] font-extrabold rounded-[8px] transition-all focus:outline-none"
              >
                Approve
              </button>
            </div>
            
          </div>
        </div>
      </div>
    </Teleport>
  </div>
</template>

<style scoped>
/* Mempercantik Scrollbar di tabel dan container */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}
::-webkit-scrollbar-track {
  background: transparent;
}
::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 10px;
}
::-webkit-scrollbar-thumb:hover {
  background: #94a3b8;
}
</style>