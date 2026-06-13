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
            <h1 class="text-xl md:text-2xl font-black text-[#19462D] tracking-tight">Supplier</h1>
            <p class="text-xs md:text-sm text-gray-500 font-medium mt-0.5">Manajemen data pemasok</p>
          </div>
        </div>
      </header>

      <div class="p-4 md:p-8 flex flex-col gap-6 w-full animate-fade">
        <section class="bg-white rounded-3xl shadow-sm border border-gray-100 p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-bold text-[#19462D] flex items-center gap-2">
              <div class="w-2 h-2 rounded-full bg-[#19462D]"></div> Data Supplier
            </h3>
            <button class="bg-blue-500 text-white p-2 rounded-xl hover:bg-blue-600 transition shadow-sm">
              <PlusIcon class="w-5 h-5" />
            </button>
          </div>
          
          <div class="overflow-x-auto no-scrollbar">
            <table class="w-full text-sm text-left min-w-[800px]">
              <thead class="text-xs text-green-800 bg-green-50 uppercase tracking-widest border-b border-gray-100">
                <tr>
                  <th class="p-4 font-black rounded-tl-lg">Nama Supplier</th>
                  <th class="p-4 font-black">Kontak/Telepon</th>
                  <th class="p-4 font-black">Komoditas</th>
                  <th class="p-4 font-black">Alamat Wilayah</th>
                  <th class="p-4 font-black text-center rounded-tr-lg">Aksi</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-50">
                <tr v-for="(item, i) in supplierData" :key="i" class="hover:bg-gray-50/50 transition-colors">
                  <td class="p-4 font-bold text-gray-800">{{ item.nama }}</td>
                  <td class="p-4 font-bold text-gray-800">{{ item.kontak }}</td>
                  <td class="p-4">
                    <span class="bg-blue-50 text-blue-600 px-3 py-1 rounded-full text-xs font-bold">{{ item.komoditas }}</span>
                  </td>
                  <td class="p-4 font-bold text-gray-800">{{ item.alamat }}</td>
                  <td class="p-4">
                    <div class="flex justify-center gap-2">
                      <button class="w-8 h-8 rounded-lg bg-yellow-50 text-yellow-500 flex items-center justify-center hover:bg-yellow-400 hover:text-white transition">
                        <EditIcon class="w-4 h-4" />
                      </button>
                      <button class="w-8 h-8 rounded-lg bg-red-50 text-red-500 flex items-center justify-center hover:bg-red-500 hover:text-white transition">
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
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { MenuIcon, PlusIcon, EditIcon, TrashIcon } from 'lucide-vue-next'
import SidebarKaryawan from '~/components/SidebarKaryawan.vue'

const isSidebarOpen = ref(false)
const supplierData = ref([
  { nama: 'Gudang Jaya', kontak: '0895-2345-6789', komoditas: 'Sembako', alamat: 'Samarinda Seberang' },
  { nama: 'Tani Makmur', kontak: '0812-9988-7766', komoditas: 'Sayuran', alamat: 'Loa Janan' },
  { nama: 'Sumber Rejeki', kontak: '0856-1122-3344', komoditas: 'Beras', alamat: 'Palaran' },
])
</script>