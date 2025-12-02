<!-- src/components/SplashScreen.vue -->
<template>
  <div
    class="fixed inset-0 flex flex-col items-center justify-center overflow-hidden bg-white p-6"
    role="region"
    aria-label="Welcome to Royal Thaal"
  >
    <!-- Animated Background Particles (subtle luxury) -->
    <div class="absolute inset-0 opacity-20">
      <div class="absolute top-10 left-10 w-72 h-72 bg-yellow-400 rounded-full mix-blend-screen filter blur-3xl animate-pulse"></div>
      <div class="absolute bottom-20 right-12 w-96 h-96 bg-orange-600 rounded-full mix-blend-screen filter blur-3xl animate-pulse delay-1000"></div>
    </div>

    <div class="relative z-10 flex flex-col items-center text-center max-w-2xl">
      <!-- Logo -->
      <div class="mb-8 animate-float">
        <img
          src="/download.png"
          alt="Royal Thaal Logo"
          class="w-28 h-28 md:w-40 md:h-40 rounded-full shadow-2xl border-4 border-[#FFD700] p-2 bg-white/10 backdrop-blur-sm"
          loading="eager"
        />
      </div>

      <!-- BIG BEAUTIFUL THAAL IMAGE (replaces the text) -->
      <div class="relative mb-10 animate-scaleIn">
        <img
          src="/thaal.jpg"
          alt="Your Royal Thaal Awaits"
          class="w-64 h-64 md:w-80 md:h-80 rounded-full object-cover border-8 border-white shadow-3xl 
                 ring-8 ring-[#FFD700]/30 hover:ring-[#FFD700]/60 transition-all duration-700
                 animate-pulse-slow"
        />
        <!-- Golden glow ring around thaal -->
        <div class="absolute inset-0 rounded-full bg-gradient-to-t from-[#FFD700]/20 to-transparent blur-3xl"></div>
      </div>

      <!-- Enhanced CTA Button -->
      <button
        @click="$emit('start')"
        @keydown.enter.prevent="$emit('start')"
        @keydown.space.prevent="$emit('start')"
        class="group relative mt-12 px-10 py-5 text-xl md:text-2xl font-bold text-black bg-gradient-to-r from-[#FFD700] via-[#FFBE0B] to-[#FB8C00] 
               rounded-full shadow-2xl hover:shadow-3xl transform hover:scale-110 transition-all duration-500 
               overflow-hidden border-4 border-[#B8860B] opacity-0 animate-fadeInUp animation-delay-1000
               focus:outline-none focus:ring-4 focus:ring-yellow-400 focus:ring-opacity-60"
        aria-label="Start building your Royal Thaal"
      >
        <span class="relative z-10 flex items-center gap-3">
          MAKE MY THAAL
          
        </span>
        <div class="absolute inset-0 -translate-x-full bg-gradient-to-r from-transparent via-white to-transparent opacity-30 skew-x-12 group-hover:animate-shine"></div>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SplashScreen',
  mounted() {
    // Preload images
    const images = ['/download.png', '/thaal.jpg'];
    images.forEach(src => {
      const img = new Image();
      img.src = src;
    });
  }
}
</script>

<style scoped>
@keyframes float {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(5deg); }
}
.animate-float { animation: float 6s ease-in-out infinite; }

@keyframes scaleIn {
  0% { transform: scale(0.3); opacity: 0; }
  80% { transform: scale(1.08); }
  100% { transform: scale(1); opacity: 1; }
}
.animate-scaleIn {
  animation: scaleIn 1.4s cubic-bezier(0.22, 1, 0.36, 1) forwards;
}

@keyframes pulse-slow {
  0%, 100% { box-shadow: 0 0 30px rgba(255, 215, 0, 0.3); }
  50% { box-shadow: 0 0 60px rgba(255, 215, 0, 0.6); }
}
.animate-pulse-slow {
  animation: pulse-slow 4s ease-in-out infinite;
}

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-fadeInUp { animation: fadeInUp 0.9s ease-out forwards; }

.animation-delay-700 { animation-delay: 0.7s; }
.animation-delay-1000 { animation-delay: 1s; }

@keyframes shine {
  0% { transform: translateX(-100%) skewX(-15deg); }
  100% { transform: translateX(300%) skewX(-15deg); }
}
.group-hover\\:animate-shine:hover {
  animation: shine 1.5s ease-in-out;
}
</style>