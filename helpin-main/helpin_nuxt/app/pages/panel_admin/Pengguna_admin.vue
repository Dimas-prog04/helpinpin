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
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Akun Pengguna</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Kelola akses dan data pengguna sistem</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full max-w-[100vw] animate-fade">
        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2">
              <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Data Akun Pengguna
            </h3>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Username</th>
                  <th class="p-4 font-black">Email</th>
                  <th class="p-4 font-black">Kontak</th>
                  <th class="p-4 font-black text-center">Status</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in penggunaData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.username }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.email }}</td>
                  <td class="p-4 font-medium text-gray-600">{{ item.kontak }}</td>
                  <td class="p-4 text-center">
                    <span :class="['px-3 py-1 text-xs font-bold rounded-full', item.status === 'Online' ? 'bg-blue-100 text-blue-700' : 'bg-red-100 text-red-700']">
                      {{ item.status }}
                    </span>
                  </td>
                  <td class="p-4 text-center">
                    <button 
                      @click="bukaDetailPengguna(item)" 
                      class="w-8 h-8 rounded-lg bg-blue-500 text-white flex items-center justify-center hover:bg-blue-600 mx-auto transition shadow-sm"
                    >
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

    <div 
      v-if="showDetailModal" 
      class="fixed inset-0 z-[100] flex items-center justify-center bg-black/40 backdrop-blur-sm p-4 overflow-y-auto" 
      @click.self="showDetailModal = false"
    >
      <div class="bg-white rounded-[32px] w-full max-w-4xl shadow-2xl relative animate-fade my-auto pb-8">
        
        <div class="flex items-center gap-4 p-6 md:p-8">
          <button 
            @click="showDetailModal = false" 
            class="p-2 hover:bg-gray-100 rounded-xl transition flex items-center justify-center"
          >
            <XIcon class="w-8 h-8 text-black" />
          </button>
          <h1 class="text-2xl md:text-3xl font-extrabold text-[#19462D]">
            Akun Pengguna
          </h1>
        </div>

        <div class="px-6 md:px-10">
          
          <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
            
            <div>
              <label class="block text-xs font-extrabold text-[#19462D] mb-2 uppercase tracking-wide">Username</label>
              <div class="w-full px-5 py-4 rounded-xl border border-gray-100 bg-gray-50/80 text-sm font-extrabold text-[#19462D]">
                {{ detailPengguna.username }}
              </div>
            </div>

            <div>
              <label class="block text-xs font-extrabold text-[#19462D] mb-2 uppercase tracking-wide">Email</label>
              <div class="w-full px-5 py-4 rounded-xl border border-gray-100 bg-gray-50/80 text-sm font-extrabold text-[#19462D]">
                {{ detailPengguna.email }}
              </div>
            </div>

            <div>
              <label class="block text-xs font-extrabold text-[#19462D] mb-2 uppercase tracking-wide">Kontak</label>
              <div class="w-full px-5 py-4 rounded-xl border border-gray-100 bg-gray-50/80 text-sm font-extrabold text-[#19462D]">
                {{ detailPengguna.kontak }}
              </div>
            </div>

          </div>

          <div>
            <label class="block text-xs font-extrabold text-[#19462D] mb-2 uppercase tracking-wide">Status</label>
            <div 
              class="inline-flex items-center justify-center px-8 py-2.5 rounded-lg font-extrabold text-sm tracking-wide"
              :class="detailPengguna.status === 'Online' ? 'bg-blue-100 text-blue-600' : 'bg-red-100 text-red-600'"
            >
              {{ detailPengguna.status }}
            </div>
          </div>

        </div>
        
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import { MenuIcon, EyeIcon, XIcon } from 'lucide-vue-next' // Tambahkan XIcon
import SidebarAdmin from '~/components/SidebarAdmin.vue'

const isSidebarOpen = ref(false)

// State Modal dan Data Detail
const showDetailModal = ref(false)
const detailPengguna = ref({
  username: '',
  email: '',
  kontak: '',
  status: ''
})

// Fungsi untuk membuka pop-up dan mengisi data
const bukaDetailPengguna = (pengguna) => {
  detailPengguna.value = {
    username: pengguna.username,
    email: pengguna.email,
    kontak: pengguna.kontak,
    status: pengguna.status
  }
  showDetailModal.value = true
}

const penggunaData = ref([
  { username: 'Giska678', email: 'giska678@gmail.com', kontak: '0895-2345-6789', status: 'Online' },
  { username: 'Budi99', email: 'budi99@gmail.com', kontak: '0812-3456-7890', status: 'Online' },
  { username: 'Siti_A', email: 'sitia@gmail.com', kontak: '0856-1122-3344', status: 'Offline' },
  { username: 'JokoSusilo', email: 'jokos@gmail.com', kontak: '0895-9988-7766', status: 'Online' },
])
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar { display: none; }
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.animate-fade { animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
</style>