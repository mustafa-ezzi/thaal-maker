<!-- src/components/SplashScreen.vue -->
<template>
  <div class="fixed inset-0 flex items-center justify-center overflow-hidden bg-amber-50">
    <!-- Subtle animated background orbs -->
    <div class="absolute inset-0 opacity-20 pointer-events-none">
      <div class="absolute top-0 left-0 w-96 h-96 bg-amber-600 rounded-full mix-blend-multiply blur-3xl animate-blob"></div>
      <div class="absolute bottom-0 right-0 w-80 h-80 bg-orange-700 rounded-full mix-blend-multiply blur-3xl animate-blob animation-delay-2000"></div>
      <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-96 h-96 bg-amber-800 rounded-full mix-blend-multiply blur-3xl animate-blob animation-delay-4000"></div>
    </div>

    <!-- Main Content -->
    <div class="relative z-10 text-center px-6">

      <!-- Hero Thaal with Logo in Center -->
      <div class="relative inline-block animate-scaleIn">
        <!-- Golden Thaal Base -->
        <div class="relative">
          <img
            src="/thaal.jpg"
            alt="Royal Thaal"
            class="w-80 h-80 sm:w-96 sm:h-96 md:w-[28rem] md:h-[28rem] rounded-full object-cover
                   shadow-2xl
                   ring-8 ring-amber-400/30"
          />

          <!-- Logo Perfectly Centered on Thaal -->
          <div class="absolute inset-0 flex items-center justify-center pointer-events-none">
            <div class="relative ">
              <div class="w-20 h-20 sm:w-30 sm:h-30 md:w-38 md:h-38 
                     bg-white rounded-full shadow-2xl  p-4
                     flex items-center justify-center">
                <img src="/download.png" alt="Royal Thaal Logo" class="w-full rounded-full h-full object-contain" />
              </div>
              <!-- Subtle glow ring around logo -->
              <div class="absolute inset-0 rounded-full blur-xl opacity-60 "></div>
            </div>
          </div>

          <!-- Outer golden glow ring around entire thaal -->
          <div class="absolute -inset-4 rounded-full  "></div>
        </div>
      </div>

      

      <!-- CTA Button -->
      <button
        @click="$emit('start')"
        class="mt-12 px-6 py-6 text-2xl md:text-3xl font-black text-white
               bg-[#8B1E3F] hover:bg-amber-800
               rounded-full shadow-2xl border-4 border-amber-500
               transform hover:scale-105 active:scale-95  transition-all duration-300
               opacity-0 animate-fadeInUp animation-delay-1200
               flex items-center gap-4 mx-auto"
      >
        MAKE MY THAAL
        <svg class="w-8 h-8" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3">
          <path d="M5 12h14M12 5l7 7-7 7" />
        </svg>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SplashScreen',
  mounted() {
    // Preload images
    ['/thaal.jpg', '/download.png'].forEach(src => {
      const img = new Image();
      img.src = src;
    });
  }
}
</script>

<style scoped>
@keyframes blob {
  0%, 100% { transform: translate(0, 0) scale(1); }
  33% { transform: translate(30px, -50px) scale(1.1); }
  66% { transform: translate(-20px, 20px) scale(0.9); }
}

.animate-blob {
  animation: blob 18s infinite ease-in-out;
}

@keyframes float {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(3deg); }
}

.animate-float {
  animation: float 6s ease-in-out infinite;
}

@keyframes scaleIn {
  0% { transform: scale(0.7); opacity: 0; }
  70% { transform: scale(1.05); }
  100% { transform: scale(1); opacity: 1; }
}

.animate-scaleIn {
  animation: scaleIn 1.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}

.animate-fadeInUp {
  animation: fadeInUp 1s ease-out forwards;
}

.animation-delay-800 { animation-delay: 0.8s; }
.animation-delay-1200 { animation-delay: 1.2s; }
.animation-delay-2000 { animation-delay: 2s; }
.animation-delay-4000 { animation-delay: 4s; }
</style>