<!-- src/components/SplashScreen.vue -->
<template>
  <div
    class="fixed inset-0 flex flex-col items-center justify-center overflow-hidden bg-gradient-to-br from-gray-50 via-white to-gray-100"
    role="region"
    aria-label="Welcome to Royal Thaal"
  >
    <!-- Sophisticated Animated Background Orbs -->
    <div class="absolute inset-0 opacity-[0.15] pointer-events-none overflow-hidden">
      <div class="absolute -top-20 -left-20 w-[600px] h-[600px] bg-gradient-to-br from-black to-gray-800 rounded-full mix-blend-multiply blur-3xl animate-blob"></div>
      <div class="absolute -bottom-20 -right-20 w-[500px] h-[500px] bg-gradient-to-br from-gray-900 to-black rounded-full mix-blend-multiply blur-3xl animate-blob animation-delay-2000"></div>
      <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[550px] h-[550px] bg-gradient-to-br from-gray-700 to-gray-900 rounded-full mix-blend-multiply blur-3xl animate-blob animation-delay-4000"></div>
    </div>

    <!-- Radial gradient overlay for depth -->
    <div class="absolute inset-0 bg-gradient-radial from-transparent via-white/50 to-white pointer-events-none"></div>

    <div class="relative z-10 flex flex-col items-center text-center w-full px-6 max-w-4xl">

      <!-- Premium Logo with Glow -->
      <div class="mb-8 md:mb-12 animate-float">
        <div class="relative">
          <!-- Glow effect -->
          <div class="absolute inset-0 bg-gradient-to-br from-black via-gray-800 to-black rounded-[2rem] blur-2xl opacity-40 animate-pulse-glow"></div>
          <img
            src="/download.png"
            alt="Royal Thaal Logo"
            class="relative w-24 h-24 sm:w-28 sm:h-28 md:w-36 md:h-36 rounded-[2rem] shadow-[0_20px_60px_rgba(0,0,0,0.4)] 
                   border-4 md:border-6 border-white bg-white p-3 backdrop-blur-xl
                   hover:scale-105 transition-all duration-700"
            loading="eager"
          />
        </div>
      </div>

      <!-- Hero Thaal Image - Stunning Presentation -->
      <div class="relative mb-10 md:mb-14 animate-scaleIn">
        <div class="relative group">
          <!-- Outer glow ring -->
          <div class="absolute -inset-6 bg-gradient-to-br from-black via-gray-800 to-black rounded-full blur-3xl opacity-30 group-hover:opacity-50 transition-opacity duration-700 animate-pulse-glow"></div>
          
          <!-- Main image -->
          <div class="relative">
            <img
              src="/thaal.jpg"
              alt="Your Royal Thaal Awaits"
              class="relative w-80 h-80 sm:w-96 sm:h-96 md:w-[28rem] md:h-[28rem] lg:w-[32rem] lg:h-[32rem] 
                     rounded-full object-cover border-[10px] md:border-[14px] border-white 
                     shadow-[0_25px_80px_-15px_rgba(0,0,0,0.5)]
                     ring-4 md:ring-6 ring-black/20 
                     transform transition-all duration-700 hover:scale-[1.02] hover:shadow-[0_30px_100px_-15px_rgba(0,0,0,0.6)]"
            />
            <div class="absolute inset-8 md:inset-10 rounded-full bg-white/30 blur-3xl"></div>
          </div>
        </div>
      </div>

      <!-- Title & Tagline - Refined Typography -->
      <div class="space-y-3 md:space-y-5 mb-10 md:mb-12 opacity-0 animate-fadeInUp animation-delay-800">
      
        
        <div class="h-[2px] w-32 md:w-40 mx-auto bg-gradient-to-r from-transparent via-black to-transparent"></div>
       
      </div>

      <!-- Premium Glassmorphism CTA Button -->
      <button
        @click="$emit('start')"
        @keydown.enter.prevent="$emit('start')"
        @keydown.space.prevent="$emit('start')"
        class="group relative px-10 py-5 md:px-14 md:py-7 text-xl md:text-2xl lg:text-3xl font-black tracking-wider text-white
               bg-gradient-to-br from-black via-gray-900 to-black
               backdrop-blur-xl border-2 border-white/20
               rounded-full shadow-[0_20px_60px_-15px_rgba(0,0,0,0.5)]
               hover:shadow-[0_25px_80px_-15px_rgba(0,0,0,0.7)]
               transform hover:scale-110 active:scale-105
               transition-all duration-500 overflow-hidden
               focus:outline-none focus:ring-4 focus:ring-black/30
               opacity-0 animate-fadeInUp animation-delay-1200"
        aria-label="Start building your Royal Thaal"
      >
        <!-- Animated gradient overlay -->
        <div class="absolute inset-0 bg-gradient-to-r from-gray-900 via-black to-gray-900 opacity-0 group-hover:opacity-100 transition-opacity duration-500"></div>
        
        <span class="relative z-10 flex items-center justify-center gap-3 md:gap-4">
          MAKE MY THAAL
          <svg 
            class="w-6 h-6 md:w-7 md:h-7 group-hover:translate-x-2 transition-transform duration-500" 
            viewBox="0 0 24 24" 
            fill="none" 
            stroke="currentColor" 
            stroke-width="2.5"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path d="M5 12h14M12 5l7 7-7 7" />
          </svg>
        </span>

        <!-- Shine sweep effect -->
        <div class="absolute inset-0 -translate-x-full bg-gradient-to-r from-transparent via-white/30 to-transparent skew-x-12 group-hover:animate-shine"></div>
        
        <!-- Subtle pulse ring -->
        <div class="absolute inset-0 rounded-full ring-2 ring-white/0 group-hover:ring-white/20 transition-all duration-500"></div>
      </button>

    
    </div>
  </div>
</template>

<script>
export default {
  name: 'SplashScreen',
  mounted() {
    // Preload critical images for instant display
    const images = ['/download.png', '/thaal.jpg'];
    images.forEach(src => {
      const img = new Image();
      img.src = src;
    });
  }
}
</script>

<style scoped>
/* Blob Animation - Organic Movement */
@keyframes blob {
  0%, 100% { 
    transform: translate(0, 0) scale(1) rotate(0deg); 
  }
  25% { 
    transform: translate(30px, -60px) scale(1.1) rotate(5deg); 
  }
  50% { 
    transform: translate(-40px, 30px) scale(0.9) rotate(-5deg); 
  }
  75% { 
    transform: translate(60px, 60px) scale(1.05) rotate(3deg); 
  }
}

.animate-blob {
  animation: blob 20s ease-in-out infinite;
}

/* Floating Animation - Gentle Hover */
@keyframes float {
  0%, 100% { 
    transform: translateY(0) rotate(0deg); 
  }
  50% { 
    transform: translateY(-20px) rotate(2deg); 
  }
}

.animate-float {
  animation: float 8s ease-in-out infinite;
}

/* Scale In with Bounce */
@keyframes scaleIn {
  0% { 
    transform: scale(0.5); 
    opacity: 0; 
  }
  60% { 
    transform: scale(1.05); 
  }
  80% {
    transform: scale(0.98);
  }
  100% { 
    transform: scale(1); 
    opacity: 1; 
  }
}

.animate-scaleIn {
  animation: scaleIn 1.8s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

/* Pulse Glow - Subtle Breathing Effect */
@keyframes pulse-glow {
  0%, 100% { 
    opacity: 0.3;
    transform: scale(1);
  }
  50% { 
    opacity: 0.5;
    transform: scale(1.05);
  }
}

.animate-pulse-glow {
  animation: pulse-glow 4s ease-in-out infinite;
}

/* Fade In Up - Smooth Entry */
@keyframes fadeInUp {
  from { 
    opacity: 0; 
    transform: translateY(30px); 
  }
  to { 
    opacity: 1; 
    transform: translateY(0); 
  }
}

.animate-fadeInUp {
  animation: fadeInUp 1s cubic-bezier(0.22, 1, 0.36, 1) forwards;
}

/* Shine Sweep - Premium Effect */
@keyframes shine {
  0% { 
    transform: translateX(-200%) skewX(-15deg); 
  }
  100% { 
    transform: translateX(200%) skewX(-15deg); 
  }
}

.group-hover\:animate-shine {
  animation: shine 1.5s ease-out;
}

/* Animation Delays */
.animation-delay-800 { 
  animation-delay: 0.8s; 
}

.animation-delay-1200 { 
  animation-delay: 1.2s; 
}

.animation-delay-1600 { 
  animation-delay: 1.6s; 
}

.animation-delay-2000 { 
  animation-delay: 2s; 
}

.animation-delay-4000 { 
  animation-delay: 4s; 
}

/* Radial Gradient Utility */
.bg-gradient-radial {
  background-image: radial-gradient(circle at center, var(--tw-gradient-stops));
}

/* Remove tap highlight on mobile */
* {
  -webkit-tap-highlight-color: transparent;
}

/* Smooth font rendering */
body {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>