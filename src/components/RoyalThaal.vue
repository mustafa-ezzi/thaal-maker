<!-- src/components/RoyalThaalMaker.vue -->
<template>
  <div>
    <SplashScreen v-if="!started" @start="started = true" />

    <div v-else class="fixed inset-0 overflow-y-auto" :style="rootBg">
      <!-- Header -->
      <!-- <header class="text-center py-6 md:py-12">
        <img src="/download.png" alt="Royal Thaal"
          class="mx-auto mb-4 w-20 h-20 md:w-24 md:h-24 rounded-full shadow-2xl border-4 border-black p-2 bg-white/90" />
      </header> -->

      <!-- Mobile Sticky Preview -->
      <div class="md:hidden sticky top-0 z-50 backdrop-blur-2xl bg-white/95 border-b-4 border-black/20 shadow-2xl py-4">
        <div class="text-center">
          <div id="thaalPreviewMobile" ref="previewMobile" class="preview-wrapper-mobile mx-auto">
            <img src="/thaal.jpg" alt="Golden Thaal" class="thaal-img-mobile" />
            <img src="/download.png" class="center-logo-mobile" alt="Logo" />
            <img v-for="(cat, idx) in chosenCats" :key="cat" class="preview-item-mobile" :src="categoryImages[cat]"
              :style="getPreviewPositionStyleMobile(idx)" @click="showToast(selected[cat])" :alt="selected[cat]" />
          </div>
        </div>
      </div>

      <div class="main-container max-w-5xl mx-auto px-5 md:px-8 py-8">

        <!-- Step Card -->
        <div v-if="step < categories.length" id="stepBox" class="royal-card" :class="fadeClass">
          <div class="text-center mb-10">
            <h2 class="font-playfair text-5xl md:text-7xl font-black text-black tracking-tighter">
              {{ currentCategory.title }}
            </h2>
            <p class="text-gray-700 text-lg md:text-xl font-light mt-4 tracking-widest uppercase text-sm">
              Select your choice
            </p>
          </div>

          <div class="items-grid" @touchstart="handleTouchStart" @touchmove="handleTouchMove"
            @touchend="handleTouchEnd">
            <template v-for="(group, gi) in currentCategory.groups" :key="gi">
              <div class="group-header">
                <span class="header-text">{{ group.name }}</span>
              </div>
              <div v-for="(item, ii) in group.items" :key="ii" class="royal-item-card"
                :class="{ 'selected': selected[currentCategory.title] === item }"
                @click="selectItem(currentCategory.title, item)">
                <span class="item-text">{{ item }}</span>
                <div v-if="selected[currentCategory.title] === item" class="gold-check">
                  <svg viewBox="0 0 24 24" fill="none" stroke="black" stroke-width="4">
                    <path d="M6 12L10 16L18 8" />
                  </svg>
                </div>
              </div>
            </template>
          </div>

          <!-- Navigation Buttons - Glassmorphism -->
          <div class="flex flex-col sm:flex-row gap-5 justify-center mt-12 px-4">
            <button v-if="step > 0" @click="prevStep" class="glass-btn">Back</button>
            <button v-if="step < categories.length - 1" @click="nextStep" :disabled="!selected[currentCategory.title]"
              class="glass-btn">
              Next
            </button>
            <button v-else @click="finishThaal" class="glass-btn glass-btn-primary text-xl px-14 py-5 font-bold">
              Finish Thaal
            </button>
          </div>
        </div>

        <!-- Thaal Quantity Step -->
        <!-- Thaal Quantity Step – Fully Responsive -->
<div v-if="step === categories.length" class="royal-card fade-in mt-10">
  <h2 class="font-playfair text-4xl sm:text-5xl md:text-7xl font-black text-center mb-6 text-black leading-tight">
    Number of Thaals
  </h2>
  <p class="text-center text-gray-700 text-base sm:text-lg md:text-xl font-light mb-8 px-4">
    Select how many Thaals you desire
  </p>

  <!-- Quantity Selector – Super Responsive -->
  <div class="flex items-center justify-center gap-4 sm:gap-8 my-10 px-4">
    <button 
      @click="changeQuantity(-1)" 
      class="glass-btn quantity-btn text-3xl sm:text-4xl md:text-5xl px-6 sm:px-8 py-4 sm:py-5">
      −
    </button>

    <div class="text-6xl sm:text-7xl md:text-8xl font-black text-black tabular-nums tracking-tight">
      {{ thaalQuantity }}
    </div>

    <button 
      @click="changeQuantity(1)" 
      class="glass-btn quantity-btn text-3xl sm:text-4xl md:text-5xl px-6 sm:px-8 py-4 sm:py-5">
      +
    </button>
  </div>

  <!-- Action Buttons – Responsive Layout & Size -->
  <div class="flex flex-col sm:flex-row gap-4 sm:gap-6 justify-center mt-10 px-6 sm:px-4">
    <button @click="step--" class="glass-btn text-base sm:text-lg px-10 sm:px-12 py-4 sm:py-5">
      Back
    </button>
    <button @click="openSummary"
      class="glass-btn glass-btn-primary text-lg sm:text-xl px-12 sm:px-16 py-5 sm:py-6 font-bold shadow-2xl">
      Complete
    </button>
  </div>
</div>

        <!-- Desktop Preview -->
        <div class="thaal-section-desktop hidden md:block mt-16">
          <div id="thaalPreview" ref="preview" class="preview-wrapper mx-auto">
            <img src="/thaal.jpg" alt="Golden Thaal" class="thaal-img" />
            <img src="/download.png" class="center-logo" alt="Logo" />
            <img v-for="(cat, idx) in chosenCats" :key="cat" class="preview-item" :src="categoryImages[cat]"
              :style="getPreviewPositionStyle('desktop', idx)" @click="showToast(selected[cat])" :alt="selected[cat]" />
          </div>
        </div>
      </div>

      <!-- Toast -->
      <div
        class="toast fixed bottom-6 left-1/2 -translate-x-1/2 z-50 px-8 py-4 rounded-full bg-black text-white font-bold shadow-2xl text-base border-2 border-black"
        :class="{ 'show': toastMsg }">
        <span>{{ toastMsg }}</span>
      </div>

      <!-- Summary Modal -->
      <div v-if="showSummary"
        class="fixed inset-0 bg-black/90 flex justify-center z-50 p-4 pt-10 overflow-y-auto backdrop-blur-xl"
        @click.self="closeModal">
        <div
          class="royal-modal animate-scaleIn w-full max-w-md bg-white/95 backdrop-blur-xl rounded-3xl p-8 shadow-2xl border-4 border-black/30"
          style="max-height: 90vh; overflow-y: auto;">
          <h2 class="text-5xl md:text-6xl font-playfair font-black text-black text-center mb-10 leading-none">
            Your Thaal is Ready
          </h2>
          <div class="space-y-5">
            <div class="bg-black/10 rounded-2xl p-6 border border-black/20">
              <div class="font-black text-black text-2xl">Thaal Quantity</div>
              <div class="text-3xl mt-2 font-bold">{{ thaalQuantity }}</div>
            </div>
            <div v-for="(val, cat) in selected" :key="cat" class="bg-black/5 rounded-2xl p-6 border border-black/20">
              <div class="font-black text-black text-xl">{{ cat }}</div>
              <div class="text-lg mt-2 text-gray-800 font-medium">{{ val }}</div>
            </div>
          </div>
          <div class="flex flex-col sm:flex-row gap-5 mt-12">
            <button @click="sendToWhatsApp" class="glass-btn glass-btn-primary flex-1 text-xl py-6 font-bold">
              Send to WhatsApp
            </button>
            <button @click="closeModal" class="glass-btn flex-1 opacity-80 text-xl py-6">Close</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // Your full script remains unchanged — only styles updated
  import html2canvas from "html2canvas";
  import confetti from "canvas-confetti";
  import SplashScreen from "./SplashScreen.vue";

  export default {
    components: { SplashScreen },
    data() {
      return {
        thaalQuantity: 1,
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
        ], categoryImages: { Mithaas: "/icecream.jpg", Kharaas: "/kharaas.jpg", Tarkari: "/tarkaari.jpg", Roti: "/roti.jpeg", Biryani: "/biryani.jpeg", Soups: "/soup.jpg", Salads: "/salad.jpeg", "Mouth Fresheners": "/paan.jpg" }
      };
    },
    computed: {
      currentCategory() { return this.categories[this.step]; },
      chosenCats() { return Object.keys(this.selected); },
      rootBg() {
        return {
          background: '#ffffff',
          fontFamily: "'Playfair Display', 'Inter', serif",
          minHeight: '100vh',
          color: '#000000'
        };
      }
    },
    mounted() { this.updatePreview(); window.addEventListener("resize", this.debouncedUpdatePreview); },
    beforeUnmount() { window.removeEventListener("resize", this.debouncedUpdatePreview); },
    methods: {

      changeQuantity(val) {
        const newQty = this.thaalQuantity + val;
        if (newQty >= 1 && newQty <= 30) {
          this.thaalQuantity = newQty;
        }
      },

      openSummary() {
        this.showSummary = true;

        confetti({
          particleCount: 200,
          spread: 120,
          origin: { y: 0.4 }
        });
      },


      getPreviewPositionStyleMobile(idx) {
        const total = this.chosenCats.length || 1;
        if (total === 1)
          return {
            width: '64px',
            height: '64px',
            left: '50%',
            top: '50%',
            transform: 'translate(-50%, -50%)',
            border: '6px solid white'
          };

        const angle = (idx / total) * 2 * Math.PI - Math.PI / 2;
        const radius = 78;
        const center = 130;

        return {
          width: '52px',
          height: '52px',
          left: `${center + Math.cos(angle) * radius - 26}px`,
          top: `${center + Math.sin(angle) * radius - 26}px`
        };
      },

      // ← ALL YOUR OTHER METHODS (selectItem, downloadThaal, etc.)
      selectItem(cat, item) {
        if (this.isTransitioning) return;

        this.selected = { ...this.selected, [cat]: item };
        this.updatePreview();
        this.showToast(`${item} added!`);

        confetti({
          particleCount: 50,
          spread: 70,
          origin: { y: 0.6 }
        });

        if (this.step < this.categories.length - 1) {
          setTimeout(() => {
            this.step++;
            this.fadeTransition();

            // ⭐ FIX: scroll AFTER fade animation finishes
            setTimeout(() => this.scrollToTop(), 450);

          }, 400);
        }
        else {
          setTimeout(() => this.finishThaal(), 500);
        }
      },

      fadeTransition() {
        this.isTransitioning = true;
        this.fadeClass = "fade-out";

        setTimeout(() => {
          this.fadeClass = "fade-in";

          setTimeout(() => {
            this.isTransitioning = false;
            this.scrollToTop();       // ← moves here 🎉
          }, 120);

        }, 300);
      },

      nextStep() {
        if (
          this.step < this.categories.length - 1 &&
          this.selected[this.currentCategory.title]
        ) {
          this.step++;
          this.fadeTransition();
          this.scrollToTop();
        }
      },

      prevStep() {
        if (this.step > 0) {
          this.step--;
          this.fadeTransition();
          this.scrollToTop();
        }
      },

      randomThaal() {
        this.categories.forEach(c => {
          const all = c.groups.flatMap(g => g.items);
          this.selected[c.title] = all[Math.floor(Math.random() * all.length)];
        });

        this.step = 0;
        this.updatePreview();

        confetti({
          particleCount: 120,
          spread: 100
        });
      },

      resetThaal() {
        this.selected = {};
        this.step = 0;
        this.updatePreview();
        this.showToast("Thaal Reset!");
      },

      finishThaal() {
        this.step = this.categories.length;
      },

      closeModal() {
        this.showSummary = false;
      },

      showToast(msg) {
        this.toastMsg = msg;
        setTimeout(() => (this.toastMsg = ""), 3000);
      },

      getPreviewPositionStyle(view, idx) {
        const ref =
          view === "mobile" ? this.$refs.previewMobile : this.$refs.preview;

        if (!ref) return {};

        const size = Math.min(ref.offsetWidth, ref.offsetHeight);
        const radius = size * 0.36;
        const total = this.chosenCats.length || 1;
        const angle = (idx / total) * 2 * Math.PI - Math.PI / 2;
        const center = size / 2;
        const itemSize = size * 0.23;

        return {
          width: `${itemSize}px`,
          height: `${itemSize}px`,
          left: `${center + Math.cos(angle) * radius - itemSize / 2}px`,
          top: `${center + Math.sin(angle) * radius - itemSize / 2}px`,
          transform: "translateZ(0)"
        };
      },

      updatePreview() {
        this.$nextTick(() => { });
      },

      async downloadThaal() {
        const preview =
          window.innerWidth < 768
            ? this.$refs.previewMobile
            : this.$refs.preview;

        const canvas = await html2canvas(preview, {
          scale: 2,
          backgroundColor: null,
          logging: false
        });

        const link = document.createElement("a");
        link.download = `royal-thaal-${Date.now()}.png`;
        link.href = canvas.toDataURL();
        link.click();
      },

      scrollToTop() {
        const container = this.$el.querySelector('.fixed.inset-0');

        if (container) {
          container.scrollTo({
            top: 210,
            behavior: "smooth"
          });
        }
      },


      handleTouchStart(e) {
        this.touchStartX = e.touches[0].clientX;
      },

      handleTouchMove(e) {
        if (!this.touchStartX) return;

        const touch = e.touches[0];
        const diffX = this.touchStartX - touch.clientX;
        const diffY = Math.abs(this.touchStartY - touch.clientY);

        if (diffY > 30) return;

        if (Math.abs(diffX) > 50) {
          if (diffX > 0 && this.step < this.categories.length - 1) this.nextStep();
          else if (diffX < 0 && this.step > 0) this.prevStep();
          this.touchStartX = 0;
        }
      },


      handleTouchEnd() {
        this.touchStartX = 0;
      },

      sendToWhatsApp() {
        const number = "923032614853";

        let msg = "👑 *Royal Thaal — Premium Order* 👑\n\n";
        msg += `*Thaal Quantity:* ${this.thaalQuantity}\n\n`;
        msg += "*Selections:*\n";

        let idx = 1;
        for (const cat in this.selected) {
          msg += `${idx}. *${cat}:* ${this.selected[cat]}\n`;
          idx++;
        }


        const url = `https://wa.me/${number}?text=${encodeURIComponent(msg)}`;
        window.open(url, "_blank");
      },


      debouncedUpdatePreview: (() => {
        let t;
        return function () {
          clearTimeout(t);
          t = setTimeout(() => this.updatePreview(), 100);
        };
      })()
    }

  };
</script>

<style scoped>
  /* Glassmorphism Buttons - Ultra Premium */
  .glass-btn {
    background: rgba(255, 255, 255, 0.25);
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
    border: 1.5px solid rgba(0, 0, 0, 0.2);
    color: #000;
    font-weight: 800;
    font-size: 1.1rem;
    padding: 14px 32px;
    border-radius: 20px;
    transition: all 0.4s cubic-bezier(0.23, 1, 0.32, 1);
    box-shadow:
      0 8px 32px rgba(0, 0, 0, 0.15),
      inset 0 2px 8px rgba(255, 255, 255, 0.4);
    letter-spacing: 0.5px;
    position: relative;
    overflow: hidden;
  }

  .glass-btn::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(135deg, rgba(255, 255, 255, 0.3) 0%, rgba(255, 255, 255, 0.05) 100%);
    opacity: 0;
    transition: opacity 0.4s;
  }

  .glass-btn:hover:not(:disabled)::before {
    opacity: 1;
  }

  .glass-btn:hover:not(:disabled) {
    transform: translateY(-6px);
    background: rgba(255, 255, 255, 0.4);
    border-color: rgba(0, 0, 0, 0.4);
    box-shadow:
      0 20px 50px rgba(0, 0, 0, 0.25),
      inset 0 2px 12px rgba(255, 255, 255, 0.6);
  }

  .glass-btn:active {
    transform: translateY(-2px);
  }

  /* Primary Action Button (Complete, Finish, WhatsApp) */
  .glass-btn-primary {
    background: rgba(0, 0, 0, 0.85);
    color: white;
    border: 1.5px solid rgba(0, 0, 0, 0.6);
    box-shadow:
      0 10px 40px rgba(0, 0, 0, 0.3),
      inset 0 2px 10px rgba(255, 255, 255, 0.2);
  }

  .glass-btn-primary:hover:not(:disabled) {
    background: rgba(0, 0, 0, 0.95);
    border-color: black;
    box-shadow:
      0 25px 60px rgba(0, 0, 0, 0.4),
      inset 0 3px 15px rgba(255, 255, 255, 0.3);
    transform: translateY(-8px);
  }

  .glass-btn:disabled {
    opacity: 0.4;
    cursor: not-allowed;
    transform: none !important;
  }

  /* Rest of your styles (cards, preview, etc.) */
  .main-container {
    display: flex;
    flex-direction: column;
    gap: 32px;
  }

  @media (min-width: 900px) {
    .main-container {
      display: grid;
      grid-template-columns: 1fr 460px;
      gap: 60px;
    }
  }

  .items-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 18px;
    touch-action: pan-y;
  }

  @media (min-width: 480px) {
    .items-grid {
      grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    }
  }

  .preview-wrapper {
    width: 90vw;
    height: 90vw;
    max-width: 420px;
    max-height: 420px;
    margin: 0 auto;
    position: relative;
    border-radius: 50%;
    overflow: hidden;
    border: 0px solid transparent;
    box-shadow: 0 50px 120px rgba(0, 0, 0, 0.25);
  }

  .preview-wrapper-mobile {
    width: 280px;
    height: 280px;
    margin: 0 auto;
    position: relative;
    border-radius: 50%;
    overflow: hidden;
    border: 12px solid transparent;
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.25);
    background: white;
  }

  .preview-item,
  .preview-item-mobile {
    position: absolute;
    border-radius: 50%;
    border: 3px solid rgb(155, 155, 155);
    box-shadow: 0 20px 50px rgba(0, 0, 0, 0.2);
    object-fit: cover;
    transition: all 0.4s ease;
  }

  .preview-item:hover,
  .preview-item-mobile:hover {
    transform: scale(1.35);
    border-color: black;
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.35);
    z-index: 50;
  }

  .royal-card {
    background: rgba(255, 255, 255, 0.96);
    backdrop-filter: blur(20px);
    border: 1px solid rgba(0, 0, 0, 0.15);
    border-radius: 36px;
    padding: 48px 32px;
    box-shadow: 0 30px 100px rgba(0, 0, 0, 0.12);
  }

  .group-header {
    grid-column: 1 / -1;
    margin: 40px 0 20px;
    padding: 16px 24px;
    background: black;
    color: white;
    font-size: 16px;
    font-weight: 900;
    text-transform: uppercase;
    letter-spacing: 2px;
    text-align: center;
    border-radius: 12px;
  }

  .royal-item-card {
    background: rgba(0, 0, 0, 0.06);
    border: 1px solid rgba(0, 0, 0, 0.2);
    border-radius: 24px;
    padding: 24px 18px;
    text-align: center;
    font-weight: 600;
    font-size: 15px;
    color: black;
    cursor: pointer;
    transition: all 0.4s ease;
    backdrop-filter: blur(10px);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  }

  .royal-item-card:hover {
    transform: translateY(-10px);
    background: rgba(0, 0, 0, 0.12);
    border-color: black;
  }

  .royal-item-card.selected {
    background: black;
    color: white;
    transform: scale(1.07);
    border-color: black;
    box-shadow: 0 0 50px rgba(0, 0, 0, 0.3);
  }

  .gold-check {
    position: absolute;
    top: 12px;
    right: 12px;
    width: 36px;
    height: 36px;
    background: black;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.4);
  }

  .center-logo,
  .center-logo-mobile {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    object-fit: contain;
    z-index: 30;
    border-radius: 50%;
    border: 12px solid transparent;
    opacity: 0.92;
  }

  .center-logo {
    width: 38%;
    height: 38%;
  }

  .center-logo-mobile {
    width: 42%;
    height: 42%;
  }

  .toast {
    transform: translateY(100px);
    opacity: 0;
    transition: all 0.6s ease;
  }

  .toast.show {
    transform: translateY(0);
    opacity: 1;
  }

  @keyframes scaleIn {
    from {
      transform: scale(0.8);
      opacity: 0;
    }

    to {
      transform: scale(1);
      opacity: 1;
    }
  }

  .animate-scaleIn {
    animation: scaleIn 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  /* Responsive Quantity Buttons – Extra Polish */
.quantity-btn {
  min-width: 56px;
  min-height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  padding: 0 !important;
  aspect-ratio: 1;
  font-weight: 300;
}

@media (max-width: 480px) {
  .quantity-btn {
    min-width: 50px;
    min-height: 50px;
    font-size: 2.2rem !important;
  }
}
</style>