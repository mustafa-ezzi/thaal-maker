<!-- src/components/RoyalThaalMaker.vue -->
<template>
  <div>
    <SplashScreen v-if="!started" @start="started = true" />

    <div v-else class="fixed inset-0 overflow-y-auto" :style="rootBg">
      <!-- Header -->
      <header class="text-center py-6 md:py-12">
        <img src="/download.png" alt="Royal Thaal"
          class="mx-auto mb-4 w-20 h-20 md:w-24 md:h-24 rounded-full shadow-2xl border-4 border-[#FFD700] p-2 bg-white/10" />
      </header>

      <!-- Mobile Sticky Preview - Compact & Perfect -->
      <div
        class="md:hidden sticky top-0 z-50 bg-gradient-to-b from-[#8B0000]/98 to-[#A11212]/95 backdrop-blur-xl border-b-4 border-[#FFD700]/30 shadow-2xl py-4">
        <div class="text-center">
          <div id="thaalPreviewMobile" ref="previewMobile" class="preview-wrapper-mobile mx-auto">
            <img src="/thaal.jpg" alt="Golden Thaal" class="thaal-img-mobile" />
            <img v-for="(cat, idx) in chosenCats" :key="cat" class="preview-item-mobile" :src="categoryImages[cat]"
              :style="getPreviewPositionStyleMobile(idx)" @click="showToast(selected[cat])" :alt="selected[cat]" />
          </div>
          <p class="text-[#FFD700] font-bold text-sm mt-2 tracking-wider">Your Royal Thaal</p>
        </div>
        <div class="flex justify-center gap-3 mt-3 px-4">
          <button @click="randomThaal" class="gold-btn-mini text-xs px-4 py-2">Random</button>
          <button @click="downloadThaal" class="gold-btn-mini text-xs px-4 py-2">Download</button>
          <button @click="resetThaal" class="gold-btn-mini text-xs px-4 py-2 opacity-80">Reset</button>
        </div>
      </div>

      <div class="main-container max-w-7xl mx-auto px-4 md:px-8 pb-10">

        <!-- Step Card -->
        <div id="stepBox" class="royal-card" :class="fadeClass">
          <!-- Progress Bar -->
          <div class="progress-bar relative mb-8 flex justify-between items-center px-2">
            <div v-for="(cat, i) in categories" :key="i"
              class="progress-step w-12 h-12 md:w-16 md:h-16 rounded-full flex items-center justify-center text-2xl md:text-3xl font-black shadow-2xl border-4 border-[#FFD700]"
              :class="{
                'bg-gradient-to-br from-[#FFD700] to-[#FFA500] text-black scale-125 ring-8 ring-[#FFD700]/40': i === step,
                'bg-gradient-to-br from-[#FFD700] to-[#FFBE0B] text-black opacity-90': i < step,
                'bg-[#8B0000]/70 text-[#FFD700]': i > step
              }">
              <i :class="`fas fa-${cat.icon}`"></i>
            </div>
            <div
              class="absolute top-1/2 left-14 right-14 md:left-16 md:right-16 h-2 -translate-y-1/2 bg-[#FFD700]/20 rounded-full overflow-hidden">
              <div class="h-full bg-gradient-to-r from-[#FFD700] to-[#FFA500] transition-all duration-1000"
                :style="{ width: `${((step + 1) / categories.length) * 100}%` }"></div>
            </div>
          </div>

          <h2 class="text-center font-playfair text-4xl md:text-6xl font-black text-[#FFD700] mb-3 drop-shadow-lg">
            {{ currentCategory.title }}
          </h2>
          <p class="text-center text-[#FFE4B5] text-lg md:text-xl font-bold mb-8 tracking-wider">
            Choose your royal selection
          </p>

          <!-- Food Items Grid -->
          <div class="items-grid" @touchstart="handleTouchStart" @touchmove="handleTouchMove"
            @touchend="handleTouchEnd">
            <template v-for="(group, gi) in currentCategory.groups" :key="gi">
              <div class="group-header">{{ group.name }}</div>
              <div v-for="(item, ii) in group.items" :key="ii" class="royal-item-card"
                :class="{ 'selected': selected[currentCategory.title] === item }"
                @click="selectItem(currentCategory.title, item)">
                <span class="item-text">{{ item }}</span>
                <div v-if="selected[currentCategory.title] === item" class="gold-check">
                  <svg viewBox="0 0 24 24" fill="none">
                    <path d="M6 12L10 16L18 8" stroke="black" stroke-width="4" stroke-linecap="round"
                      stroke-linejoin="round" />
                  </svg>
                </div>
              </div>
            </template>
          </div>

          <!-- Navigation Buttons - FIXED FOR MOBILE -->
          <div class="flex flex-wrap justify-center gap-4 mt-10 px-4">
            <button v-if="step > 0" @click="prevStep" class="gold-btn w-full sm:w-auto text-lg px-10 py-4">Back</button>
            <button v-if="step < categories.length - 1" @click="nextStep" :disabled="!selected[currentCategory.title]"
              class="gold-btn w-full sm:w-auto text-lg px-10 py-4">Next</button>
            <button v-else @click="finishThaal" class="gold-btn w-full sm:w-auto text-xl px-12 py-5">Finish
              Thaal</button>
          </div>
        </div>

        <!-- Desktop Preview -->
        <div class="thaal-section-desktop hidden md:block mt-12">
          <div id="thaalPreview" ref="preview" class="preview-wrapper mx-auto">
            <img src="/thaal.jpg" alt="Golden Thaal" class="thaal-img" />
            <img v-for="(cat, idx) in chosenCats" :key="cat" class="preview-item" :src="categoryImages[cat]"
              :style="getPreviewPositionStyle('desktop', idx)" @click="showToast(selected[cat])" :alt="selected[cat]" />
          </div>
          <div class="flex justify-center gap-6 mt-8">
            <button @click="randomThaal" class="gold-btn">Random Thaal</button>
            <button @click="downloadThaal" class="gold-btn">Download Image</button>
            <button @click="resetThaal" class="gold-btn opacity-90">Reset</button>
          </div>
        </div>
      </div>

      <!-- Toast -->
      <div
        class="toast fixed bottom-6 left-1/2 -translate-x-1/2 z-50 px-6 py-3 rounded-full bg-gradient-to-r from-[#FFD700] to-[#FFA500] text-black font-bold shadow-2xl text-base"
        :class="{ 'show': toastMsg }">
        <span>{{ toastMsg }}</span>
      </div>

      <!-- Summary Modal - FIXED BUTTONS -->
      <div v-if="showSummary"
        class="fixed inset-0 bg-black/90 flex items-center justify-center z-50 p-4 backdrop-blur-sm"
        @click.self="closeModal">
        <div class="royal-modal animate-scaleIn w-full max-w-md">
          <h2 class="text-4xl md:text-5xl font-playfair font-black text-[#FFD700] text-center mb-8 leading-tight">
            Your Royal Thaal is Ready!
          </h2>
          <div class="space-y-4 max-h-96 overflow-y-auto">
            <div v-for="(val, cat) in selected" :key="cat"
              class="bg-white/10 backdrop-blur-lg border-2 border-[#FFD700]/50 rounded-2xl p-5 text-[#FFE4B5]">
              <div class="font-bold text-[#FFD700] text-xl">{{ cat }}</div>
              <div class="text-lg mt-1">{{ val }}</div>
            </div>
          </div>
          <div class="flex flex-col sm:flex-row gap-4 mt-10">
            <button @click="downloadThaal" class="gold-btn flex-1 text-xl py-5">Download</button>
            <button @click="closeModal" class="gold-btn flex-1 opacity-80 text-xl py-5">Close</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // Your full script (methods, data, etc.) remains EXACTLY THE SAME as before
  // Only the template and a few styles were fixed for mobile buttons
  import html2canvas from "html2canvas";
  import confetti from "canvas-confetti";
  import SplashScreen from "./SplashScreen.vue";

  export default {
    components: { SplashScreen },
    data() {
      return {
        started: false, step: 0, selected: {}, fadeClass: "fade-in", showSummary: false, toastMsg: "", isTransitioning: false, touchStartX: 0,
  categories: [
          {
            title: "Mithaas",
            icon: "ice-cream",
            groups: [
              { name: "Normal Icecream Flavours", items: ["Peshawri Icecream", "Chocolate Icecream", "Strawberry Icecream", "Blueberry Icecream", "Rose Peshawri Icecream", "Rose Rabri Icecream", "Kinnu Rabri Icecream (Seasonal)", "Peach Rabri Icecream (Seasonal)", "Mango Icecream"] },
              { name: "Premium Flavours", items: ["Milco Icecream", "Ferrero Rocher", "Cocktail Rabri", "After Eight", "Pina Colada", "Chocolate Fudge", "Snickers", "Hot Lava Cakes", "Hot Brownies"] },
              { name: "Halwa", items: ["Gajar Halwa", "Suji Halwa", "Chuwara Halwa", "Lauki Halwa", "Anjeer Halwa"] },
              { name: "Other", items: ["Ghewar", "Malai Malai Khaja", "3 Milk Cake"] }
            ]
          },
          {
            title: "Kharaas",
            icon: "drumstick-bite",
            groups: [
              { name: "BBQ - Chicken", items: ["Chicken Behari Tikka", "Chicken Balochi Tikka", "Chicken Malai Tikka", "Chicken Tarragon Steaks", "Mushroom Steaks", "Chicken Afghani Boti", "Chicken Malai Boti", "Turkish Kabab", "Dynamite Chicken"] },
              { name: "BBQ - Mutton", items: ["Grilled Mutton Leg", "Mutton Adana Kabab"] },
              { name: "Fried - Chicken", items: ["Chicken Bhora Fry", "Chicken Lahori Chargha", "Chicken Cheese Fillet", "Mushroom Chicken", "Chicken Rollade", "Chicken Cheese Tempura", "Chicken Tempura", "Shangrila Chicken"] },
              { name: "Fried - Mutton", items: ["Roasted Mutton Leg"] }
            ]
          },
          {
            title: "Tarkari",
            icon: "pot-food",
            groups: [
              { name: "Chicken Tarkari", items: ["Chicken Makhni Handi", "Chicken Cheese Handi", "Chicken Afghani Handi", "Chicken Achari", "Chicken Karahi", "Chicken Coriander Handi", "Chicken Cheese Malai Boti Handi", "Chicken Cheese Kabab Handi", "Malai Kofta Tarkari"] },
              { name: "Mutton Tarkari", items: ["Mutton Karahi", "Mutton Handi", "Mutton Paya", "Mutton Kunna", "Mutton Nehari", "Mutton Achari"] }
            ]
          },
          {
            title: "Roti",
            icon: "bread-slice",
            groups: [
              { name: "Roti Selection", items: ["Mohabbat Ni Roti (12 pcs)", "Plain Naan Live (6 pcs)", "Garlic Naan Live (6 pcs)", "Donuts Live (8 pcs)", "BBQ Paratha Live", "Kulcha (Readymade)", "Suji Paratha", "Besan Paratha", "Naan"] }
            ]
          },
          {
            title: "Biryani",
            icon: "bowl-rice",
            groups: [
              { name: "Chicken Rice Dishes", items: ["Chicken Biryani", "Chicken Akhni", "Chicken Schezwan Rice", "Chicken Shashlik Rice", "Chicken Chilli Dry", "Chicken BBQ Platter"] },
              { name: "Mutton Rice Dishes", items: ["Mutton Saffron Biryani", "Mutton Sindhi Biryani", "Mutton Bombay Biryani", "Mutton Curren Pulao", "Mutton Akhni Pulao", "Mutton Leg Mandi Rice"] }
            ]
          },
          {
            title: "Soups",
            icon: "bowl-hot",
            groups: [
              { name: "Soups", items: ["Kashmir Soup", "Almond Cashew Soup", "Coconut Soup", "Yakhni", "Mushroom Soup", "Tomato Soup", "Chicken Corn Soup"] }
            ]
          },
          {
            title: "Salads",
            icon: "carrot",
            groups: [
              { name: "Salads", items: ["Dahi Boondi", "Sev Puri", "Tang Salad (Fruit Chaat)", "Russian Sauce Salad", "Thousand Island Salad", "Fresh Salad", "Channa Bateta", "Kathyawari Channa"] }
            ]
          },
          {
            title: "Mouth Fresheners",
            icon: "leaf",
            groups: [
              { name: "Paan & Others", items: ["Paan", "Dry Fruits Paan", "Chocolate Paan", "Gola Churan", "Masala Kishmish", "Namkeen Churan", "Sweet Churan", "Paan Bahar"] },
              { name: "Fruits", items: ["Kinnu Mosambee Bowl (Seasonal)", "Pomegranate/Grapefruit (Seasonal)"] },
              { name: "Drinks", items: ["Chai Live", "Coffee", "Kahwa", "Sharbat"] }
            ]
          }
        ],        categoryImages: { Mithaas: "/icecream.jpg", Kharaas: "/kharaas.jpg", Tarkari: "/tarkaari.jpg", Roti: "/roti.jpeg", Biryani: "/biryani.jpeg", Soups: "/soup.jpg", Salads: "/salad.jpeg", "Mouth Fresheners": "/paan.jpg" }
      };
    },
    computed: {
      currentCategory() { return this.categories[this.step]; }, chosenCats() { return Object.keys(this.selected); },
      rootBg() { return { background: `linear-gradient(rgba(139,0,0,0.85), rgba(139,0,0,0.95)), url('/background.jpg'), linear-gradient(135deg,#8B0000 0%,#A11212 50%,#DC143C 100%)`, backgroundSize: 'cover', backgroundPosition: 'center top', backgroundRepeat: 'no-repeat', backgroundAttachment: 'scroll', fontFamily: "'Playfair Display', 'Inter', serif", minHeight: '100vh' }; }
    },
    mounted() { this.updatePreview(); window.addEventListener("resize", this.debouncedUpdatePreview); },
    beforeUnmount() { window.removeEventListener("resize", this.debouncedUpdatePreview); },
    methods: {
      getPreviewPositionStyleMobile(idx) {
        const total = this.chosenCats.length || 1;
        if (total === 1) return { width: '64px', height: '64px', left: '50%', top: '50%', transform: 'translate(-50%, -50%)', border: '6px solid white' };
        const angle = (idx / total) * 2 * Math.PI - Math.PI / 2;
        const radius = 78; const center = 130;
        return { width: '52px', height: '52px', left: `${center + Math.cos(angle) * radius - 26}px`, top: `${center + Math.sin(angle) * radius - 26}px` };
      },
      // ← ALL YOUR OTHER METHODS (selectItem, downloadThaal, etc.) — keep exactly as before
      selectItem(cat, item) {
        if (this.isTransitioning) return; this.selected = { ...this.selected, [cat]: item }; this.updatePreview(); this.showToast(`${item} added!`); confetti({ particleCount: 50, spread: 70, origin: { y: 0.6 } });
        if (this.step < this.categories.length - 1) setTimeout(() => { this.step++; this.fadeTransition(); this.scrollToTop(); }, 400);
        else setTimeout(() => this.finishThaal(), 500);
      },
      fadeTransition() { this.isTransitioning = true; this.fadeClass = "fade-out"; setTimeout(() => { this.fadeClass = "fade-in"; setTimeout(() => this.isTransitioning = false, 100); }, 300); },
      nextStep() { if (this.step < this.categories.length - 1 && this.selected[this.currentCategory.title]) { this.step++; this.fadeTransition(); this.scrollToTop(); } },
      prevStep() { if (this.step > 0) { this.step--; this.fadeTransition(); this.scrollToTop(); } },
      randomThaal() { this.categories.forEach(c => { const all = c.groups.flatMap(g => g.items); this.selected[c.title] = all[Math.floor(Math.random() * all.length)]; }); this.step = 0; this.updatePreview(); confetti({ particleCount: 120, spread: 100 }); },
      resetThaal() { this.selected = {}; this.step = 0; this.updatePreview(); this.showToast("Thaal Reset!"); },
      finishThaal() { this.showSummary = true; confetti({ particleCount: 200, spread: 120, origin: { y: 0.4 } }); },
      closeModal() { this.showSummary = false; },
      showToast(msg) { this.toastMsg = msg; setTimeout(() => this.toastMsg = "", 3000); },
      getPreviewPositionStyle(view, idx) { const ref = view === 'mobile' ? this.$refs.previewMobile : this.$refs.preview; if (!ref) return {}; const size = Math.min(ref.offsetWidth, ref.offsetHeight); const radius = size * 0.36; const total = this.chosenCats.length || 1; const angle = (idx / total) * 2 * Math.PI - Math.PI / 2; const center = size / 2; const itemSize = size * 0.23; return { width: `${itemSize}px`, height: `${itemSize}px`, left: `${center + Math.cos(angle) * radius - itemSize / 2}px`, top: `${center + Math.sin(angle) * radius - itemSize / 2}px`, transform: 'translateZ(0)' }; },
      updatePreview() { this.$nextTick(() => { }); },
      async downloadThaal() { const preview = window.innerWidth < 768 ? this.$refs.previewMobile : this.$refs.preview; const canvas = await html2canvas(preview, { scale: 2, backgroundColor: null, logging: false }); const link = document.createElement("a"); link.download = `royal-thaal-${Date.now()}.png`; link.href = canvas.toDataURL(); link.click(); },
      scrollToTop() { document.getElementById('stepBox')?.scrollIntoView({ behavior: 'smooth', block: 'start' }); },
      handleTouchStart(e) { this.touchStartX = e.touches[0].clientX; },
      handleTouchMove(e) { if (!this.touchStartX) return; const diff = this.touchStartX - e.touches[0].clientX; if (Math.abs(diff) > 50) { if (diff > 0 && this.step < this.categories.length - 1) this.nextStep(); else if (diff < 0 && this.step > 0) this.prevStep(); this.touchStartX = 0; } },
      handleTouchEnd() { this.touchStartX = 0; },
      debouncedUpdatePreview: (() => { let t; return function () { clearTimeout(t); t = setTimeout(() => this.updatePreview(), 100); }; })()
    }
  };
</script>

<style scoped>
  /* Same beautiful royal styles + mobile fixes */
  :root {
    --gold: #FFD700;
    --gold-2: #FFA500;
    --maroon: #8B0000;
    --light-gold: #FFE4B5;
  }

  .fixed.inset-0 {
    background-attachment: scroll !important;
  }

  @media (min-width: 768px) {
    .fixed.inset-0 {
      background-attachment: fixed;
    }
  }

  .main-container {
    display: flex;
    flex-direction: column;
    gap: 32px;
  }

  @media (min-width: 900px) {
    .main-container {
      display: grid;
      grid-template-columns: 1fr 440px;
      gap: 50px;
    }
  }

  .royal-card {
    background: rgba(139, 0, 0, 0.45);
    backdrop-filter: blur(20px);
    border: 3px solid var(--gold);
    border-radius: 32px;
    padding: 32px 20px;
    box-shadow: 0 25px 80px rgba(0, 0, 0, 0.7);
  }

  .items-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 16px;
    touch-action: pan-y;
  }

  @media (min-width: 480px) {
    .items-grid {
      grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    }
  }

  .group-header {
    grid-column: 1/-1;
    font-weight: 900;
    font-size: 17px;
    color: var(--gold);
    margin: 20px 0 8px;
    padding-left: 10px;
    border-left: 5px solid var(--gold);
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  .royal-item-card {
    background: rgba(255, 255, 255, 0.08);
    border: 2px solid transparent;
    color: #FFE4B5;
    padding: 20px 14px;
    border-radius: 20px;
    text-align: center;
    font-weight: 700;
    font-size: 15px;
    cursor: pointer;
    transition: all 0.4s ease;
    position: relative;
    overflow: hidden;
    backdrop-filter: blur(12px);
  }

  .royal-item-card:hover {
    transform: translateY(-8px);
    border-color: var(--gold);
    box-shadow: 0 20px 40px rgba(255, 215, 0, 0.35);
  }

  .royal-item-card.selected {
    background: linear-gradient(135deg, rgba(255, 215, 0, 0.3), rgba(255, 165, 0, 0.3));
    border-color: var(--gold);
    transform: scale(1.07);
    box-shadow: 0 0 50px rgba(255, 215, 0, 0.7);
  }

  .gold-check {
    position: absolute;
    top: 8px;
    right: 8px;
    width: 34px;
    height: 34px;
    background: var(--gold);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.4);
  }

  /* Buttons */
  .gold-btn,
  .gold-btn-mini {
    background: linear-gradient(135deg, var(--gold), var(--gold-2));
    color: black;
    font-weight: 900;
    border-radius: 18px;
    border: 3px solid #B8860B;
    box-shadow: 0 10px 30px rgba(255, 215, 0, 0.5);
    transition: all 0.3s ease;
  }

  .gold-btn {
    padding: 14px 20px;
    min-width: 140px;
    font-size: 17px;
  }

  .gold-btn-mini {
    padding: 10px 16px;
    font-size: 13px;
    min-width: 80px;
  }

  .gold-btn:hover,
  .gold-btn-mini:hover {
    transform: translateY(-4px);
    box-shadow: 0 18px 40px rgba(255, 215, 0, 0.6);
  }

  /* Thaal Previews */
  .preview-wrapper {
    width: 90vw;
    height: 90vw;
    max-width: 400px;
    max-height: 400px;
    margin: 0 auto;
    position: relative;
    border-radius: 50%;
    overflow: hidden;
    border: 14px solid var(--gold);
    box-shadow: 0 40px 100px rgba(0, 0, 0, 0.8);
  }

  .thaal-img,
  .thaal-img-mobile {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 50%;
  }

  .preview-item {
    position: absolute;
    border-radius: 50%;
    border: 7px solid white;
    box-shadow: 0 15px 40px rgba(0, 0, 0, 0.8);
    object-fit: cover;
    transition: 0.4s;
  }

  .preview-item:hover {
    transform: scale(1.3);
    z-index: 40;
  }

  /* Mobile Compact Preview */
  .preview-wrapper-mobile {
    width: 260px;
    height: 260px;
    max-width: 280px;
    max-height: 280px;
    margin: 0 auto;
    position: relative;
    border-radius: 50%;
    overflow: hidden;
    border: 10px solid var(--gold);
    box-shadow: 0 20px 50px rgba(0, 0, 0, 0.8), inset 0 0 40px rgba(255, 215, 0, 0.2);
  }

  .preview-item-mobile {
    position: absolute;
    width: 52px !important;
    height: 52px !important;
    border-radius: 50%;
    border: 5px solid white;
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.7);
    object-fit: cover;
    transition: all 0.3s ease;
    z-index: 10;
  }

  .preview-item-mobile:hover {
    transform: scale(1.4) !important;
    z-index: 50 !important;
    border-color: var(--gold);
  }

  /* Modal & Animations */
  .toast {
    transform: translateY(100px);
    opacity: 0;
    transition: all 0.5s ease;
  }

  .toast.show {
    transform: translateY(0);
    opacity: 1;
  }

  .royal-modal {
    background: linear-gradient(135deg, rgba(139, 0, 0, 0.97), rgba(161, 18, 18, 0.97));
    border: 5px solid var(--gold);
    border-radius: 36px;
    padding: 40px 24px;
    box-shadow: 0 40px 120px rgba(255, 215, 0, 0.5);
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translateY(30px);
    }

    to {
      opacity: 1;
      transform: none;
    }
  }

  .fade-in {
    animation: fadeIn 0.6s ease-out;
  }

  .fade-out {
    animation: fadeIn 0.6s ease-out reverse;
  }

  @keyframes scaleIn {
    from {
      transform: scale(0.7);
      opacity: 0;
    }

    to {
      transform: scale(1);
      opacity: 1;
    }
  }

  .animate-scaleIn {
    animation: scaleIn 0.7s cubic-bezier(0.34, 1.56, 0.64, 1);
  }
</style>