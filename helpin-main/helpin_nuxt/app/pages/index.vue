<script setup lang="ts">
import { ref, shallowRef, onMounted, onUnmounted, nextTick } from 'vue'

// ==========================================
// 1. STATE & LOGIC: DROPDOWN LOGIN
// ==========================================
const isLoginDropdownOpen = ref(false)
const isScrolled = ref(false)

const handleScroll = () => {
  isScrolled.value = window.scrollY > 50
}

// ==========================================
// 2. STATE & LOGIC: SCROLL ANIMATION (DIRECTIVE)
// ==========================================
const vReveal = {
  mounted: (el: HTMLElement, binding: any) => {
    const delayClass = binding.value?.delay || 'delay-0'
    const directionClass = binding.value?.direction || 'translate-y-12'
    
    el.classList.add('opacity-0', directionClass, 'transition-all', 'duration-1000', 'ease-[cubic-bezier(0.25,0.1,0.25,1)]', delayClass)
    
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          el.classList.remove('opacity-0', directionClass)
          el.classList.add('opacity-100', 'translate-y-0', 'translate-x-0')
          observer.unobserve(el)
        }
      })
    }, { threshold: 0.15, rootMargin: '0px 0px -50px 0px' })
    
    observer.observe(el)
  }
}

// ==========================================
// 3. STATE & LOGIC: 3D CAROUSEL SLIDER
// ==========================================
const features = ref([
  { id: 1, title: 'AI Pencatatan Panen', desc: 'Sistem cerdas yang otomatis merekap hasil pertanian secara real-time langsung dari sawah.', icon: 'M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15' },
  { id: 2, title: 'AI Problem Solver', desc: 'Asisten virtual berbasis machine learning yang membantu menganalisis kesehatan hewan ternak.', icon: 'M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z' },
  { id: 3, title: 'Koperasi Transparan', desc: 'Otomatisasi pembagian Sisa Hasil Usaha (SHU), manajemen kas simpan pinjam anggota tanpa kesalahan.', icon: 'M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z' }
])

const currentIndex = ref(1)
const nextSlide = () => currentIndex.value = (currentIndex.value + 1) % features.value.length
const prevSlide = () => currentIndex.value = (currentIndex.value - 1 + features.value.length) % features.value.length

const getSlideClass = (index: number) => {
  if (index === currentIndex.value) return 'active-slide z-20 opacity-100 center-slide pointer-events-auto border-green-200 bg-white shadow-2xl shadow-green-900/10'
  if (index === (currentIndex.value - 1 + features.value.length) % features.value.length) return 'prev-slide z-10 opacity-40 rotate-y-left pointer-events-auto bg-gray-50'
  if (index === (currentIndex.value + 1) % features.value.length) return 'next-slide z-10 opacity-40 rotate-y-right pointer-events-auto bg-gray-50'
  return 'hidden opacity-0 pointer-events-none'
}

// ==========================================
// 4. STATE & LOGIC: LEAFLET MAP
// ==========================================
const mapContainer = ref<HTMLElement | null>(null)
const map = shallowRef<any>(null)

const koperasiLocations = [
  { id: 1, name: 'KUD Tani Makmur Utama', type: 'Pertanian Padi & Palawija', lat: -0.5022, lng: 117.1536, members: 145, status: 'Aktif AI', address: 'Jl. Poros Makroman, Kaltim' },
  { id: 2, name: 'Koperasi Susu Sejahtera', type: 'Peternakan Sapi Perah', lat: -0.4284, lng: 116.9853, members: 85, status: 'Aktif AI', address: 'Kawasan Agrowisata Kukar' },
  { id: 3, name: 'KUD Agro Jaya', type: 'Perkebunan Sawit Mandiri', lat: -1.2379, lng: 116.8529, members: 310, status: 'Proses Integrasi', address: 'Km. 15 Karang Joang' }
]

// ==============================================================
// 5. STATE & LOGIC: TECH STACK MARQUEE + FOCAL ZOOM
// ==============================================================
const techStack = [
  { name: 'Nuxt.js', desc: 'Frontend modern & cepat', src: 'https://cdn.worldvectorlogo.com/logos/nuxt-2.svg' },
  { name: 'Rust (Axum)', desc: 'Backend performa tinggi', src: 'https://cdn.worldvectorlogo.com/logos/rust.svg' },
  { name: 'Python', desc: 'AI & Data Processing', src: 'https://cdn.worldvectorlogo.com/logos/python-5.svg' },
  { name: 'PostgreSQL', desc: 'Database Relasional', src: 'https://www.vectorlogo.zone/logos/postgresql/postgresql-icon.svg' },
  { name: 'MongoDB', desc: 'Database NoSQL', src: 'https://cdn.worldvectorlogo.com/logos/mongodb-icon-1.svg' }
]

const techTrackRef = ref<HTMLElement | null>(null)
let rafId: number;

const tickFocalZoom = () => {
  if (!techTrackRef.value) {
    rafId = requestAnimationFrame(tickFocalZoom);
    return;
  }

  const all = techTrackRef.value.querySelectorAll('.tech-card-item');
  if (!all.length) {
    rafId = requestAnimationFrame(tickFocalZoom);
    return;
  }

  const cx = window.innerWidth / 2;
  let ci = 0, cd = Infinity;

  all.forEach((c, i) => {
    const rect = c.getBoundingClientRect();
    const d = Math.abs((rect.left + rect.width / 2) - cx);
    if (d < cd) { 
      cd = d; 
      ci = i; 
    }
  });

  const mi = ci % techStack.length;

  all.forEach((c, i) => {
    const isCenter = (i % techStack.length) === mi;
    const el = c as HTMLElement;
    
    el.style.transform = 'scale(' + (isCenter ? 1.06 : 0.88) + ')';
    el.style.opacity = isCenter ? '1' : '0.5';
    el.style.filter = isCenter ? 'grayscale(0) drop-shadow(0 0 15px rgba(34,197,94,0.3))' : 'grayscale(1)';
  });

  rafId = requestAnimationFrame(tickFocalZoom);
}

// ==========================================
// LIFECYCLE HOOKS
// ==========================================
onMounted(async () => {
  window.addEventListener('scroll', handleScroll)
  
  if (window.innerWidth > 768) {
    rafId = requestAnimationFrame(tickFocalZoom);
  }

  if (process.client) {
    await nextTick()
    setTimeout(async () => {
      if (!mapContainer.value) return
      const L = await import('leaflet')
      delete (L.Icon.Default.prototype as any)._getIconUrl;
      L.Icon.Default.mergeOptions({
        iconRetinaUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-icon-2x.png',
        iconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-icon.png',
        shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',
      });
      map.value = L.map(mapContainer.value).setView([-0.75, 117.0], 8)
      L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', { maxZoom: 19 }).addTo(map.value)
      koperasiLocations.forEach(loc => {
        const marker = L.marker([loc.lat, loc.lng]).addTo(map.value)
        const statusColor = loc.status === 'Aktif AI' ? '#166534' : '#854d0e';
        const statusBg = loc.status === 'Aktif AI' ? '#dcfce7' : '#fef08a';
        const popupContent = `<div style="min-width: 220px; font-family: 'Inter', system-ui, sans-serif;"><h4 style="margin: 0 0 4px 0; font-size: 15px; font-weight: 800; color: #1c4532;">${loc.name}</h4><p style="margin: 0 0 12px 0; font-size: 12px; color: #6b7280;">${loc.address}</p><div style="border-top: 1px solid #e5e7eb; padding-top: 10px; margin-bottom: 12px; display: flex; flex-direction: column; gap: 4px;"><div style="display: flex; justify-content: space-between; font-size: 12px;"><span style="color: #6b7280;">Fokus:</span><strong style="color: #374151;">${loc.type}</strong></div></div><span style="display: inline-block; width: 100%; text-align: center; background-color: ${statusBg}; color: ${statusColor}; padding: 6px 0; border-radius: 6px; font-size: 11px; font-weight: 700;">${loc.status}</span></div>`
        marker.bindPopup(popupContent)
      })
      setTimeout(() => { if (map.value) map.value.invalidateSize() }, 200)
    }, 200)
  }
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  cancelAnimationFrame(rafId)
})
</script>

<template>
  <div class="relative overflow-hidden w-full min-h-screen bg-grid-pattern text-gray-800 antialiased font-sans selection:bg-green-200 selection:text-green-900">
    
    <div class="fixed inset-0 w-full h-full pointer-events-none z-[1]">
      <div class="absolute top-0 left-[5%] md:left-[15%] w-[350px] h-[350px] bg-green-500/40 rounded-full blur-[120px] animate-fall-left"></div>
      <div class="absolute top-0 right-[5%] md:right-[15%] w-[450px] h-[450px] bg-[#a4d233]/30 rounded-full blur-[140px] animate-fall-right"></div>
    </div>

    <header class="fixed top-5 left-0 right-0 z-[100] px-4 sm:px-6 lg:px-8 pointer-events-none">
      <div :class="[
        'container mx-auto max-w-7xl rounded-full transition-all duration-500 pointer-events-auto py-3 px-6 md:px-8 border',
        isScrolled ? 'bg-white shadow-xl border-gray-100' : 'bg-white/10 backdrop-blur-xl border-white/20 shadow-2xl'
      ]">
        <div class="grid grid-cols-2 lg:grid-cols-3 items-center w-full">
          
          <nav class="hidden lg:flex items-center gap-8 text-sm font-semibold transition-colors duration-300">
            <a href="#beranda" :class="isScrolled ? 'text-gray-900' : 'text-white'" class="hover:text-green-600">Beranda</a>
            <a href="#fitur" :class="isScrolled ? 'text-gray-600' : 'text-gray-300'" class="hover:text-green-600">Fitur AI</a>
            <a href="#testimoni" :class="isScrolled ? 'text-gray-600' : 'text-gray-300'" class="hover:text-green-600">Testimoni</a>
          </nav>
          
          <div class="flex justify-center">
            <img src="/assets/helpin_light_logo.png" alt="Logo Helpin" class="h-9 w-auto transition-all duration-300" :class="isScrolled ? '' : 'brightness-0 invert'" />
          </div>

          <div class="flex items-center justify-end gap-5">
            
            <div class="relative">
              <button 
                @click="isLoginDropdownOpen = !isLoginDropdownOpen"
                class="text-sm font-bold transition flex items-center gap-1.5 focus:outline-none"
                :class="isScrolled ? 'text-gray-800 hover:text-green-600' : 'text-white hover:text-green-300'"
              >
                Masuk
                <svg class="w-4 h-4 transition-transform duration-300" :class="{ 'rotate-180': isLoginDropdownOpen }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M19 9l-7 7-7-7"></path>
                </svg>
              </button>

              <div v-if="isLoginDropdownOpen" @click="isLoginDropdownOpen = false" class="fixed inset-0 z-40 cursor-default"></div>

              <Transition name="fade-down">
                <div v-if="isLoginDropdownOpen" class="absolute top-full right-0 mt-4 w-56 bg-white rounded-2xl shadow-2xl border border-gray-100 overflow-hidden z-50 py-3 text-gray-800">
                  
                  <div class="px-3 pb-2 border-b border-gray-50 mb-2">
                    <span class="px-3 text-[10px] font-extrabold text-gray-400 uppercase tracking-widest">Sistem Panel</span>
                    
                    <div class="mt-1 flex flex-col">
                      <NuxtLink to="/panel_admin/dashboard_admin" @click="isLoginDropdownOpen = false" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-bold text-gray-600 hover:bg-purple-50 hover:text-purple-700 transition-colors">
                        <div class="w-6 h-6 rounded-md bg-purple-100 flex items-center justify-center text-purple-600">
                          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z"></path></svg>
                        </div>
                        Panel Admin
                      </NuxtLink>

                      <NuxtLink to="/panel_karyawan/dashboard_karyawan" @click="isLoginDropdownOpen = false" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-bold text-gray-600 hover:bg-teal-50 hover:text-teal-700 transition-colors">
                        <div class="w-6 h-6 rounded-md bg-teal-100 flex items-center justify-center text-teal-600">
                          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 13.255A23.931 23.931 0 0112 15c-3.183 0-6.22-.62-9-1.745M16 6V4a2 2 0 00-2-2h-4a2 2 0 00-2 2v2m4 6h.01M5 20h14a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path></svg>
                        </div>
                        Panel Karyawan
                      </NuxtLink>

                      <NuxtLink to="/panel_petani/dashboard_petani" @click="isLoginDropdownOpen = false" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-bold text-gray-600 hover:bg-green-50 hover:text-[#0F8901] transition-colors">
                        <div class="w-6 h-6 rounded-md bg-green-100 flex items-center justify-center text-green-600">
                          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"></path></svg>
                        </div>
                        Panel Petani
                      </NuxtLink>
                      
                      <NuxtLink to="/panel_peternak/dashboard_peternak" @click="isLoginDropdownOpen = false" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-bold text-gray-600 hover:bg-orange-50 hover:text-orange-700 transition-colors">
                        <div class="w-6 h-6 rounded-md bg-orange-100 flex items-center justify-center text-orange-600">
                          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"></path></svg>
                        </div>
                        Panel Peternak
                      </NuxtLink>

                      <NuxtLink to="/panel_auditore/dashboard_auditore" @click="isLoginDropdownOpen = false" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-bold text-gray-600 hover:bg-indigo-50 hover:text-indigo-700 transition-colors">
                        <div class="w-6 h-6 rounded-md bg-indigo-100 flex items-center justify-center text-indigo-600">
                          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4"></path>
                          </svg>
                        </div>
                        Panel Auditor
                      </NuxtLink>
                    </div>
                  </div>

                  <div class="px-3 mt-1">
                    <NuxtLink to="/ecommerce/beranda" @click="isLoginDropdownOpen = false" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-bold text-gray-600 hover:bg-blue-50 hover:text-blue-700 transition-colors">
                      <div class="w-6 h-6 rounded-md bg-blue-100 flex items-center justify-center text-blue-600">
                        <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"></path></svg>
                      </div>
                      E-Commerce
                    </NuxtLink>
                  </div>

                </div>
              </Transition>
            </div>
            
            <button class="bg-gradient-to-r from-[#0F8901] from-70% to-[#042400] text-white px-6 py-2.5 rounded-full font-bold text-sm shadow-lg shadow-green-600/40 hover:scale-105 transition-all">
              Mulai Gratis
            </button>

          </div>
        </div>
      </div>
    </header>

    <section id="beranda" class="relative min-h-[90vh] flex items-center overflow-hidden pt-28 pb-20 bg-[#1c4532]">
     <img 
  src="/vd_bg.gif" 
  alt="Background Animasi" 
  class="absolute inset-0 w-full h-full object-cover z-0 pointer-events-none" 
/>
      
      <div class="container mx-auto max-w-7xl px-6 lg:px-10 xl:px-11 grid lg:grid-cols-11 gap-12 items-center relative z-10">
        <div class="flex flex-col items-start text-left lg:col-span-6 xl:col-span-6 mt-10 lg:mt-0">
          <div v-reveal="{ direction: 'translate-y-8' }" class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full bg-white/10 border border-white/20 text-[10px] md:text-xs font-bold text-white uppercase tracking-widest mb-6 shadow-sm backdrop-blur-md">
            <span class="w-2 h-2 rounded-full bg-green-400 animate-pulse"></span> Generasi AI Tercepat v99.0
          </div>
          <h1 v-reveal="{ direction: 'translate-y-8', delay: 'delay-100' }" class="text-5xl md:text-7xl lg:text-[5rem] font-extrabold text-white leading-[1.1] mb-8 drop-shadow-2xl">
            Koperasi Maju<br />Berbasis AI,<br /><span class="bg-gradient-to-r from-[#0F8901] from-70% to-[#042400] bg-clip-text text-transparent drop-shadow-lg">Akselerasi Bisnis.</span>
          </h1>
          <p v-reveal="{ direction: 'translate-y-8', delay: 'delay-200' }" class="text-gray-100 text-lg md:text-xl mb-10 max-w-lg leading-relaxed font-medium drop-shadow-md">
            Platform Koperasi AI tercanggih yang mengubah Pencatatan Manual menjadi <span class="text-white font-extrabold drop-shadow-sm">Otomatis</span> tanpa ribet, siap pakai, and dioptimalkan.
          </p>
          
          <div v-reveal="{ direction: 'translate-y-8', delay: 'delay-300' }" class="flex flex-wrap gap-4">
            <button class="bg-gradient-to-r from-[#0F8901] to-[#042400] text-white px-6 py-2.5 rounded-full font-bold text-base hover:bg-green-500 transition-all shadow-lg shadow-green-600/40">Mulai Coba Gratis</button>
            <button class="bg-white/10 backdrop-blur-md border border-white/20 text-white px-8 py-4 rounded-full font-bold text-base hover:bg-white/20 transition-all flex items-center gap-2 shadow-lg">
              <svg class="w-5 h-5 drop-shadow-md" fill="currentColor" viewBox="0 0 20 20"><path d="M2 10a8 8 0 1116 0 8 8 0 01-16 0zm6.392-2.992l4.5 3a1 1 0 010 1.984l-4.5 3A1 1 0 017 14V6a1 1 0 011.392-.992z"/></svg> Lihat Demo
            </button>
          </div>
        </div>
      </div>
    </section>

    <section class="border-y border-green-100/50 py-16 md:py-20 relative z-20 backdrop-blur-sm overflow-hidden">
      <div class="container mx-auto px-6 max-w-7xl relative z-20">
        <div class="text-center mb-12">
          <span class="bg-[#edf5e6] text-[#2b5a3f] text-xs font-black px-5 py-1.5 rounded-full uppercase tracking-widest inline-block mb-3 border border-green-100/80">TECH</span>
          <h2 class="text-xl md:text-2xl font-extrabold text-[#1c4532]">Teknologi Yang Digunakan</h2>
        </div>
      </div>

      <div class="relative w-full overflow-hidden flex items-center py-8 z-10">
        
        <div ref="techTrackRef" class="flex w-max animate-scroll-rtl hover:[animation-play-state:paused]">
          
          <div class="flex gap-[20px] px-[10px]">
            <div v-for="(tech, i) in techStack" :key="'A'+i" class="tech-card-item w-[280px] shrink-0 bg-white/90 backdrop-blur-md border border-green-100 rounded-3xl p-6 shadow-xl flex flex-col items-center justify-center text-center">
              <div class="h-16 mb-5 flex items-center justify-center">
                <img :src="tech.src" :alt="tech.name" loading="lazy" draggable="false" class="max-h-full w-auto object-contain drop-shadow-sm">
              </div>
              <h4 class="text-xl font-bold text-gray-800 mb-2">{{ tech.name }}</h4>
              <p class="text-sm text-gray-500 font-medium">{{ tech.desc }}</p>
            </div>
          </div>

          <div class="flex gap-[20px] px-[10px]">
            <div v-for="(tech, i) in techStack" :key="'B'+i" class="tech-card-item w-[280px] shrink-0 bg-white/90 backdrop-blur-md border border-green-100 rounded-3xl p-6 shadow-xl flex flex-col items-center justify-center text-center">
              <div class="h-16 mb-5 flex items-center justify-center">
                <img :src="tech.src" :alt="tech.name" loading="lazy" draggable="false" class="max-h-full w-auto object-contain drop-shadow-sm">
              </div>
              <h4 class="text-xl font-bold text-gray-800 mb-2">{{ tech.name }}</h4>
              <p class="text-sm text-gray-500 font-medium">{{ tech.desc }}</p>
            </div>
          </div>

        </div>

        <div class="absolute inset-y-0 left-0 w-32 md:w-64 bg-gradient-to-r from-[#f2fcf5] to-transparent z-20 pointer-events-none"></div>
        <div class="absolute inset-y-0 right-0 w-32 md:w-64 bg-gradient-to-l from-[#f2fcf5] to-transparent z-20 pointer-events-none"></div>

      </div>
    </section>

    <section id="fitur" class="py-28 overflow-hidden perspective-1500 relative z-10">
      <div v-reveal="{ direction: 'translate-y-8' }" class="text-center mb-20 px-6 relative z-10">
        <span class="bg-white/80 text-green-800 text-xs font-extrabold px-4 py-1.5 rounded-full uppercase tracking-widest inline-block mb-4 shadow-sm border border-green-200 backdrop-blur-md">Infrastruktur Inti</span>
        <h2 class="text-3xl md:text-5xl font-extrabold text-[#1c4532]">Otomatisasi Menggunakan AI</h2>
      </div>
      <div v-reveal="{ direction: 'translate-y-12', delay: 'delay-100' }" class="relative w-full max-w-6xl mx-auto h-[420px] flex items-center justify-center px-4 z-10">
        <div class="absolute inset-0 w-full h-full flex items-center justify-center transform-style-preserve-3d pointer-events-none">
          <div v-for="(feature, index) in features" :key="feature.id" class="absolute w-[320px] sm:w-[400px] md:w-[480px] bg-white/90 backdrop-blur-md rounded-[32px] p-8 md:p-10 transition-all duration-700 ease-out border shadow-lg" :class="getSlideClass(index)">
            <div class="flex flex-col gap-6">
              <div class="flex items-center justify-between">
                <div class="w-14 h-14 rounded-2xl bg-green-50 flex items-center justify-center text-green-600 border border-green-100"><svg class="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="feature.icon" /></svg></div>
                <div class="flex items-center gap-2 bg-gray-50 px-3 py-1.5 rounded-full border border-gray-100"><span class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></span><span class="text-xs font-bold text-gray-500">Real-time</span></div>
              </div>
              <div>
                <h3 class="text-2xl font-extrabold text-gray-800 mb-4">{{ feature.title }}</h3>
                <p class="text-sm md:text-base text-gray-600 leading-relaxed">{{ feature.desc }}</p>
              </div>
            </div>
          </div>
        </div>
        <button @click="prevSlide" class="absolute left-4 md:left-12 z-[60] bg-white text-[#1c4532] hover:bg-green-50 p-4 rounded-full shadow-lg border border-gray-100 transition-transform hover:scale-110 focus:outline-none"><svg class="w-6 h-6 stroke-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15 19l-7-7 7-7"/></svg></button>
        <button @click="nextSlide" class="absolute right-4 md:right-12 z-[60] bg-white text-[#1c4532] hover:bg-green-50 p-4 rounded-full shadow-lg border border-gray-100 transition-transform hover:scale-110 focus:outline-none"><svg class="w-6 h-6 stroke-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7"/></svg></button>
      </div>
      <div class="flex justify-center items-center gap-2 mt-8 relative z-[60]">
        <button v-for="(f, i) in features" :key="i" @click="currentIndex = i" class="h-2 rounded-full transition-all duration-300 focus:outline-none" :class="i === currentIndex ? 'bg-green-600 w-8' : 'bg-green-300 w-2 hover:bg-green-400'" :aria-label="`Slide ${i + 1}`"></button>
      </div>
    </section>

    <section id="testimoni" class="py-24 relative overflow-hidden z-10">
      <div class="container mx-auto px-6 max-w-5xl relative z-10">
        <div v-reveal="{ direction: 'translate-y-8' }" class="text-center mb-16">
          <h2 class="text-3xl md:text-5xl font-extrabold text-[#1c4532]">Pantauan Ekosistem Aktif</h2>
          <p class="text-green-900/70 mt-4 max-w-2xl mx-auto font-medium">Kami mengelola data dari berbagai klaster tani dan peternakan di seluruh wilayah dengan presisi tinggi.</p>
        </div>
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-10 items-center">
          <div v-reveal="{ direction: 'translate-x-[-20px]' }" class="lg:col-span-7 bg-white/80 backdrop-blur-md p-3 rounded-[32px] border border-green-100 shadow-xl shadow-green-900/5">
            <ClientOnly fallback-tag="div" fallback="Memuat Peta Interaktif...">
              <div ref="mapContainer" class="w-full aspect-video rounded-[24px] overflow-hidden relative z-0 shadow-sm" style="min-height: 400px;"></div>
            </ClientOnly>
          </div>
          <div v-reveal="{ direction: 'translate-x-[20px]', delay: 'delay-100' }" class="lg:col-span-5 flex flex-col justify-center">
            <div class="bg-white/90 backdrop-blur-md p-10 rounded-[32px] border border-green-100 shadow-2xl shadow-green-900/10 relative group transition-transform duration-300">
              <div class="relative z-10">
                <p class="text-gray-700 text-lg md:text-xl font-medium leading-relaxed mb-8">"Sistem otomatisasi yang menghemat biaya operasional kami. UI/UX-nya luar biasa intuitif!"</p>
                <div class="flex items-center gap-4">
                  <div class="w-14 h-14 rounded-full bg-[#1c4532] text-white flex items-center justify-center font-bold text-lg shadow-md">SA</div>
                  <div><h4 class="font-extrabold text-gray-900 text-lg">Siti Aminah</h4><p class="text-sm text-green-600 font-semibold">Ketua Koperasi Susu Makmur</p></div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="py-12 relative z-20 bg-gradient-to-br from-[#0F8901] to-[#042400] overflow-hidden border-y border-green-900">
      <div class="absolute top-0 right-0 w-64 h-64 bg-green-600/20 rounded-full blur-3xl translate-x-1/2 -translate-y-1/2"></div>
      <div class="container mx-auto px-6 max-w-6xl relative z-10">
        <div class="flex flex-col md:flex-row justify-between items-center gap-12 py-8">
          <div v-reveal="{ direction: 'translate-y-10' }" class="text-center w-full">
            <h3 class="text-4xl md:text-5xl font-black text-white mb-2">150k+</h3>
            <p class="text-xs font-bold text-green-300 uppercase tracking-widest">Koperasi Terbantu</p>
          </div>
          <div class="hidden md:block w-px h-16 bg-white/20"></div>
          <div v-reveal="{ direction: 'translate-y-10', delay: 'delay-100' }" class="text-center w-full">
            <h3 class="text-4xl md:text-5xl font-black text-white mb-2">Rp 2.4T</h3>
            <p class="text-xs font-bold text-green-300 uppercase tracking-widest">Volume Transaksi</p>
          </div>
          <div class="hidden md:block w-px h-16 bg-white/20"></div>
          <div v-reveal="{ direction: 'translate-y-10', delay: 'delay-200' }" class="text-center w-full">
            <h3 class="text-4xl md:text-5xl font-black text-white mb-2">3x Lipat</h3>
            <p class="text-xs font-bold text-green-300 uppercase tracking-widest">Efisiensi Biaya</p>
          </div>
        </div>
      </div>
    </section>

    <section class="py-32 text-center relative overflow-hidden z-10">
      <div class="container mx-auto px-6 max-w-4xl relative z-10">
        <div v-reveal="{ direction: 'translate-y-12' }" class="bg-white/90 backdrop-blur-xl p-12 md:p-20 rounded-[40px] shadow-2xl shadow-green-900/10 border border-green-100 relative overflow-hidden group">
            <div class="absolute -top-32 -right-32 w-80 h-80 bg-green-100 rounded-full blur-3xl opacity-80 pointer-events-none group-hover:scale-125 transition-transform duration-700"></div>
            <div class="absolute -bottom-32 -left-32 w-80 h-80 bg-[#a4d233]/10 rounded-full blur-3xl opacity-80 pointer-events-none group-hover:scale-125 transition-transform duration-700"></div>
            
            <h2 class="text-3xl md:text-5xl font-extrabold bg-gradient-to-r from-[#0F8901] to-[#042400] bg-clip-text text-transparent relative z-10">
              Integrasikan HELPIN Ke Koperasi Anda
            </h2>
            <p class="text-base md:text-lg text-gray-600 font-medium mb-10 max-w-2xl mx-auto relative z-10 leading-relaxed">
              Biarkan sistem cerdas kami menangani pencatatan dan analisis harian.
            </p>
            
            <button class="bg-gradient-to-r from-[#0F8901] from-70% to-[#042400] text-white px-12 py-5 rounded-full font-bold text-lg hover:scale-105 transition-all shadow-xl shadow-green-900/40 inline-flex items-center gap-3 relative z-10">
              Mulai Sekarang Gratis
            </button>
        </div>
      </div>
    </section>

  </div>
</template>

<style scoped>
@import url('https://unpkg.com/leaflet@1.9.4/dist/leaflet.css');

.bg-grid-pattern {
  background-color: #f2fcf5; 
  background-image: 
    linear-gradient(to right, rgba(22, 163, 74, 0.06) 1px, transparent 1px),
    linear-gradient(to bottom, rgba(22, 163, 74, 0.06) 1px, transparent 1px);
  background-size: 50px 50px; 
  background-position: center top;
}

/* Transisi Focal Zoom */
.tech-card-item {
  transition: transform 0.4s cubic-bezier(0.25, 1, 0.5, 1), opacity 0.4s ease, filter 0.4s ease;
  will-change: transform, opacity, filter;
}

/* Animasi Marquee */
@keyframes scroll-rtl {
  0% { transform: translateX(0); }
  100% { transform: translateX(-50%); } 
}

.animate-scroll-rtl {
  animation: scroll-rtl 35s linear infinite;
}

.perspective-1500 { perspective: 1500px; }
.transform-style-preserve-3d { transform-style: preserve-3d; }
.delay-0 { transition-delay: 0ms; }
.delay-100 { transition-delay: 100ms; }
.delay-200 { transition-delay: 200ms; }
.delay-300 { transition-delay: 300ms; }

/* ==========================================
   ANIMASI BAYANGAN JATUH (TEMBUS LAYAR)
   ========================================== */
@keyframes fallShadow {
  0% {
    transform: translateY(-50vh);
    opacity: 0;
  }
  20% {
    opacity: 0.6;
  }
  80% {
    opacity: 0.6;
  }
  100% {
    transform: translateY(120vh);
    opacity: 0;
  }
}

.animate-fall-left {
  animation: fallShadow 12s linear infinite; 
}

.animate-fall-right {
  animation: fallShadow 12s linear infinite;
  animation-delay: 6s; 
}

/* Slide Carousel */
.active-slide, .prev-slide, .next-slide {
  transition: transform 0.7s cubic-bezier(0.2, 0.8, 0.2, 1), opacity 0.7s ease, box-shadow 0.5s ease;
  will-change: transform, opacity;
}
.center-slide { transform: translateX(0) scale(1) rotateY(0deg); }
.rotate-y-left { transform: translateX(-60%) scale(0.85) rotateY(15deg); }
.rotate-y-right { transform: translateX(60%) scale(0.85) rotateY(-15deg); }

@media (min-width: 768px) {
  .rotate-y-left { transform: translateX(-85%) scale(0.85) rotateY(25deg); }
  .rotate-y-right { transform: translateX(85%) scale(0.85) rotateY(-25deg); }
}

:deep(.leaflet-popup-content-wrapper) { border-radius: 16px; border: 1px solid #f3f4f6; padding: 0; overflow: hidden; }
:deep(.leaflet-popup-content) { margin: 16px; }
:deep(.leaflet-popup-tip-container) { display: none; }

.fade-down-enter-active, .fade-down-leave-active { transition: opacity 0.25s ease, transform 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
.fade-down-enter-from, .fade-down-leave-to { opacity: 0; transform: translateY(-10px); }
</style>