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
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Akun Karyawan</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Kelola data karyawan koperasi</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw]">
        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2">
              <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Data Karyawan
            </h3>
            
            <button @click="bukaModalTambah" class="bg-blue-500 hover:bg-blue-600 text-white p-2 rounded-lg transition shadow-sm">
              <PlusIcon class="w-5 h-5" />
            </button>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Username</th>
                  <th class="p-4 font-black">Nama Lengkap</th>
                  <th class="p-4 font-black">Kontak</th>
                  <th class="p-4 font-black text-center">Status</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in karyawanData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.username }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4 font-medium text-gray-600">{{ item.kontak }}</td>
                  <td class="p-4 text-center">
                    <span :class="['px-3 py-1 text-xs font-bold rounded-full', item.status === 'Online' ? 'bg-blue-100 text-blue-700' : 'bg-red-100 text-red-700']">
                      {{ item.status }}
                    </span>
                  </td>
                  <td class="p-4">
                    <div class="flex items-center justify-center gap-2">
                      <button @click="bukaModalEdit(item)" class="w-8 h-8 rounded-lg bg-yellow-400 text-white flex items-center justify-center hover:bg-yellow-500 transition shadow-sm">
                        <EditIcon class="w-4 h-4" />
                      </button>
                      
                      <button @click="showDeleteModal = true" class="w-8 h-8 rounded-lg bg-red-500 text-white flex items-center justify-center hover:bg-red-600 transition shadow-sm">
                        <TrashIcon class="w-4 h-4" />
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

    <div 
      v-if="showKaryawanModal" 
      class="fixed inset-0 z-[100] flex items-center justify-center bg-black/40 backdrop-blur-sm p-4 overflow-y-auto" 
      @click.self="showKaryawanModal = false"
    >
      <div class="bg-white rounded-[32px] w-full max-w-3xl shadow-2xl relative animate-fade my-auto">
        
        <div class="flex items-center gap-4 p-6 md:p-8 md:pb-6">
          <button 
            @click="showKaryawanModal = false" 
            class="p-2 hover:bg-gray-100 rounded-xl transition flex items-center justify-center"
          >
            <XIcon class="w-8 h-8 text-black" />
          </button>
          <h1 class="text-2xl md:text-3xl font-extrabold text-[#19462D]">
            {{ modalMode === 'tambah' ? 'Akun Karyawan' : 'Edit' }}
          </h1>
        </div>

        <div class="px-6 pb-6 md:px-8 md:pb-8">
          <div class="border border-gray-100 bg-[#F8FAFC] rounded-[24px] p-6 md:p-8">
            
            <div class="flex items-center gap-3 mb-6 text-xs font-extrabold tracking-widest text-[#19462D] uppercase">
              <span class="w-2.5 h-2.5 rounded-full bg-[#19462D]"></span> 
              {{ modalMode === 'tambah' ? 'TAMBAH KARYAWAN BARU' : 'EDIT DATA KARYAWAN' }}
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-x-6 gap-y-6">
              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Username</label>
                <input 
                  v-model="formKaryawan.username"
                  type="text" 
                  placeholder="Contoh : admin_koperasi" 
                  class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm focus:outline-none focus:ring-2 focus:ring-[#19462D]/20 focus:border-[#19462D] transition" 
                />
              </div>

              <div>
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Nama Lengkap</label>
                <input 
                  v-model="formKaryawan.namaLengkap"
                  type="text" 
                  placeholder="Contoh : ujang kedu.." 
                  class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm focus:outline-none focus:ring-2 focus:ring-[#19462D]/20 focus:border-[#19462D] transition" 
                />
              </div>

              <div class="md:col-span-2">
                <label class="block text-xs font-bold text-[#19462D] mb-2 uppercase">Kontak</label>
                <input 
                  v-model="formKaryawan.kontak"
                  type="text" 
                  placeholder="Contoh : 08966...." 
                  class="w-full px-4 py-3.5 rounded-xl border border-gray-200 bg-white text-sm focus:outline-none focus:ring-2 focus:ring-[#19462D]/20 focus:border-[#19462D] transition" 
                />
              </div>
            </div>

            <button 
              @click="simpanDataKaryawan"
              class="w-full mt-8 py-4 rounded-2xl bg-gradient-to-b from-[#2A5E3D] to-[#12361F] text-white font-extrabold tracking-widest hover:opacity-90 active:scale-[0.99] transition shadow-lg uppercase text-sm"
            >
              {{ modalMode === 'tambah' ? 'SIMPAN AKUN' : 'SIMPAN' }}
            </button>

          </div>
        </div>
      </div>
    </div>

    <div v-if="showDeleteModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/50 backdrop-blur-sm">
      <div class="bg-white rounded-2xl w-full max-w-sm p-6 text-center shadow-xl animate-fade">
        <div class="w-16 h-16 bg-red-50 text-red-500 rounded-full flex items-center justify-center mx-auto mb-4"><TrashIcon class="w-8 h-8" /></div>
        <h3 class="text-xl font-bold text-[#19462D] mb-6">Menghapus Informasi?</h3>
        <div class="flex gap-4">
          <button @click="showDeleteModal = false" class="flex-1 px-6 py-3 bg-gray-100 text-gray-700 font-bold rounded-lg hover:bg-gray-200 transition">BATAL</button>
          <button @click="showDeleteModal = false" class="flex-1 px-6 py-3 bg-red-500 text-white font-bold rounded-lg hover:bg-red-600 transition">YA, HAPUS</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { MenuIcon, PlusIcon, EditIcon, TrashIcon, XIcon } from 'lucide-vue-next'
import SidebarAdmin from '~/components/SidebarAdmin.vue'

const isSidebarOpen = ref(false)
const showDeleteModal = ref(false)

// --- State Karyawan Modal (Tambah/Edit) ---
const showKaryawanModal = ref(false)
const modalMode = ref('tambah') // 'tambah' atau 'edit'

// Form state reaktif untuk input
const formKaryawan = reactive({
  username: '',
  namaLengkap: '',
  kontak: ''
})

// Fungsi membuka pop-up Tambah
const bukaModalTambah = () => {
  modalMode.value = 'tambah'
  formKaryawan.username = ''
  formKaryawan.namaLengkap = ''
  formKaryawan.kontak = ''
  showKaryawanModal.value = true
}

// Fungsi membuka pop-up Edit (menerima data dari tabel)
const bukaModalEdit = (karyawan) => {
  modalMode.value = 'edit'
  // Perhatikan pemetaan field (karyawan.nama -> formKaryawan.namaLengkap)
  formKaryawan.username = karyawan.username
  formKaryawan.namaLengkap = karyawan.nama 
  formKaryawan.kontak = karyawan.kontak
  showKaryawanModal.value = true
}

// Fungsi eksekusi tombol Simpan
const simpanDataKaryawan = () => {
  if (modalMode.value === 'tambah') {
    // Jalankan logika penambahan data ke server di sini
    console.log('Menyimpan Data Baru:', formKaryawan)
    alert(`Data ditambahkan: ${formKaryawan.namaLengkap}`)
  } else {
    // Jalankan logika edit data ke server di sini
    console.log('Menyimpan Data Editan:', formKaryawan)
    alert(`Data diedit: ${formKaryawan.namaLengkap}`)
  }
  showKaryawanModal.value = false // Tutup modal setelah selesai
}

// Data dummy tabel
const karyawanData = ref([
  { username: 'admin_konsumen', nama: 'Andi Wijaya Kusuma', kontak: '0895-2345-6789', status: 'Online' },
  { username: 'admin_produsen', nama: 'Siti Aminah', kontak: '0895-2345-6789', status: 'Offline' },
  { username: 'admin_jasa', nama: 'Yayan Ruhyan', kontak: '0895-2345-6789', status: 'Online' },
])
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.3s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>  