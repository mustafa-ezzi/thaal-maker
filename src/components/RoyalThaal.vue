<!-- src/components/RoyalThaalMaker.vue -->
<template>
  <div>
    <SplashScreen v-if="!started" @start="started = true" />

    <div v-else class="fixed inset-0 overflow-y-auto bg-gradient-to-br from-amber-50 via-rose-50 to-orange-50"
      :style="rootBg">

      <!-- Mobile Sticky Preview -->
      <div
        class="md:hidden sticky top-0 z-50 backdrop-blur-2xl bg-white/90 border-b-4 border-amber-400 shadow-2xl py-4">
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
            <h2 class="font-playfair text-5xl md:text-7xl font-black text-amber-900 tracking-tighter drop-shadow-lg">
              {{ currentCategory.title }}
            </h2>
           
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
                  <svg viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="5">
                    <path d="M6 12L10 16L18 8" />
                  </svg>
                </div>
              </div>
            </template>
          </div>

          <!-- Navigation Buttons -->
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

<!-- QUANTITY STEP – RESPONSIVE & ROYAL -->
<div v-if="step === categories.length" class="royal-card fade-in mt-8 md:mt-12">
  <h2 class="font-playfair text-4xl sm:text-5xl md:text-7xl font-black text-center mb-5 md:mb-6 text-amber-900 leading-tight">
    Number of Thaals
  </h2>
  <div class="flex items-center justify-center gap-4 sm:gap-6 md:gap-8 max-w-lg mx-auto px-4 sm:px-6">
    

    <!-- Royal Input Field -->
    <div class="relative mx-2 sm:mx-4">
      <input 
        type="text" 
        v-model.number="thaalQuantity" 
        @input="sanitizeQuantity"
        @focus="$event.target.select()"
        class="thaal-input text-6xl sm:text-7xl md:text-9xl font-black text-amber-900 text-center 
               bg-transparent border-b-6 sm:border-b-8 border-amber-600 
               outline-none 
               w-32 sm:w-40 md:w-56 
               tabular-nums tracking-tight
               placeholder-amber-500/40"
        placeholder="0"
        inputmode="numeric"
      />
      <hr/>
      <!-- Golden glow underline -->
      <div class="absolute bottom-0 left-0 right-0 h-1.5 sm:h-2 bg-gradient-to-r from-transparent via-amber-400 to-transparent blur-md opacity-70"></div>
    </div>

    <!-- Plus Button -->

  </div>

  <!-- Navigation Buttons -->
  <div class="flex flex-col sm:flex-row gap-4 sm:gap-5 justify-center mt-12 md:mt-16 px-4">
     <button @click="openSummary" 
            class="glass-btn glass-btn-primary text-lg sm:text-xl md:text-2xl 
                   px-12 sm:px-16 py-5 sm:py-6 font-bold shadow-2xl 
                   hover:shadow-amber-600/40 transform hover:scale-105 
                   active:scale-95 transition-all duration-300">
      Complete Order
    </button>
    <button @click="step--" 
            class="glass-btn text-base sm:text-lg px-10 sm:px-12 py-4 sm:py-5 
                   hover:shadow-xl transition-all">
      Back
    </button>
   
  </div>
</div>

        <!-- Desktop Preview -->
        <div class="thaal-section-desktop hidden md:block mt-16">
          <div id="thaalPreview" ref="preview" class="preview-wrapper mx-auto">
            <img src="/thaal.jpg" alt="Golden Thaal" class="thaal-img" />
            <img src="/download.png" class="center-logo" alt="Logo" />
            <img v-for="(cat, idx) in chosenCats" :key="cat" class="preview-item" :src="categoryImages[cat]"
              :style="getPreviewPositionStyle('desktop', idx)" @click="showToast(selected[cat])" />
          </div>
        </div>
      </div>

      <!-- Toast -->
      <div
        class="toast fixed bottom-6 left-1/2 -translate-x-1/2 z-50 px-8 py-4 rounded-full bg-gradient-to-r from-amber-600 to-amber-700 text-white font-bold shadow-2xl text-base border-4 border-amber-400"
        :class="{ 'show': toastMsg }">
        <span>{{ toastMsg }}</span>
      </div>

      <!-- Summary Modal -->
<!-- SUMMARY MODAL – FIXED & BEAUTIFUL -->
<div v-if="showSummary" 
     class="fixed inset-0 bg-black/80 flex items-center justify-center z-50 p-4"
     @click.self="closeModal">
  
  <div class="w-full max-w-md max-h-[92vh] overflow-y-auto bg-white rounded-3xl shadow-2xl border-4 border-amber-600
              scrollbar-thin scrollbar-thumb-amber-600 scrollbar-track-amber-50">
    
    <!-- Modal Content Container -->
    <div class="p-6 sm:p-8 min-h-full flex flex-col">
      
      <!-- Header -->
      <div class="text-center mb-8">
        <h2 class="text-4xl sm:text-5xl font-playfair font-black text-amber-900 leading-tight">
          Your Royal Thaal is Ready!
        </h2>
        <div class="mt-4 w-24 h-1 bg-amber-600 mx-auto rounded-full"></div>
      </div>

      <!-- Thaal Quantity Card -->
      <div class="bg-amber-100 rounded-2xl p-6 text-center border-2 border-amber-600 shadow-inner mb-6">
        <p class="text-amber-900 font-medium uppercase tracking-wider text-sm">Thaal Quantity</p>
        <div class="text-6xl font-black text-amber-800 mt-2">{{ thaalQuantity }}</div>
      </div>

      <!-- Selected Items -->
      <div class="space-y-4 flex-1">
        <div v-for="(val, cat) in selected" :key="cat"
             class="bg-amber-50 rounded-2xl p-5 border border-amber-400 shadow-sm">
          <div class="font-bold text-amber-900 text-lg">{{ cat }}</div>
          <div class="text-amber-800 font-medium mt-1">{{ val }}</div>
        </div>
      </div>

      <!-- Action Buttons – Sticky at bottom -->
      <div class="flex flex-col sm:flex-row gap-4 mt-8 pt-4 border-t border-amber-200">
        <button @click="sendToWhatsApp"
                class="flex-1 py-5 bg-amber-700 hover:bg-amber-800 text-white font-bold text-lg rounded-full border-4 border-amber-600 shadow-xl transition transform hover:scale-105">
          Send to WhatsApp
        </button>
        <button @click="closeModal"
                class="flex-1 py-5 bg-white hover:bg-amber-50 text-amber-900 font-bold text-lg rounded-full border-4 border-amber-600 shadow-lg transition">
          Close
        </button>
      </div>
    </div>
  </div>
</div>
    </div>
  </div>
</template>

<script>
  import html2canvas from "html2canvas";
  import confetti from "canvas-confetti";
  import SplashScreen from "./SplashScreen.vue";

  export default {
    components: { SplashScreen },
    data() {
      return {
        thaalQuantity: 1,
        started: false, step: 0, selected: {}, fadeClass: "fade-in", showSummary: false, toastMsg: "", isTransitioning: false,
        touchStartX: 0, touchStartY: 0,
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
        ], categoryImages: {
          Mithaas: "/icecream.jpg", Kharaas: "/kharaas.jpg", Tarkari: "/tarkaari.jpg",
          Roti: "/roti.jpeg", Biryani: "/biryani.jpeg", Soups: "/soup.jpg",
          Salads: "/salad.jpeg", "Mouth Fresheners": "/paan.jpg"
        }
      };
    },
    computed: {
      currentCategory() { return this.categories[this.step]; },
      chosenCats() { return Object.keys(this.selected); },
      rootBg() {
        return {
          fontFamily: "'Playfair Display', 'Inter', serif",
          minHeight: '100vh',
        };
      }
    },
    mounted() { this.updatePreview(); window.addEventListener("resize", this.debouncedUpdatePreview); },
    beforeUnmount() { window.removeEventListener("resize", this.debouncedUpdatePreview); },
    methods: {

      sanitizeQuantity(e) {
  let val = e.target.value.replace(/[^0-9]/g, ''); // Only numbers
  val = val === '' ? 1 : parseInt(val);
  val = Math.max(0, Math.min(100, val)); // Clamp between 1 and 30
  this.thaalQuantity = val;
},

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
            left: '80%',
            top: '50%',
            transform: 'translate(-50%, -50%)',
            border: '6px solid white'
          };

        const angle = (idx / total) * 2 * Math.PI - Math.PI / 2;
        const radius = 78;
        const center = 130;

        return {
          width: '56px',
          height: '56px',
          left: `${center + Math.cos(angle) * radius - 15}px`,
          top: `${center + Math.sin(angle) * radius - 20}px`
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
            top: 0,
            behavior: "smooth"
          });
        }
      },


      handleTouchStart(e) {
        this.touchStartX = e.touches[0].clientX;
        this.touchStartY = e.touches[0].clientY; // ← add this
      },

      handleTouchMove(e) {
        if (!this.touchStartX || !this.touchStartY) return;

        const touch = e.touches[0];
        const diffX = this.touchStartX - touch.clientX;
        const diffY = this.touchStartY - touch.clientY;

        if (Math.abs(diffY) > Math.abs(diffX)) return; // ← ignore mostly vertical movement

        if (Math.abs(diffX) > 50) {
          if (diffX > 0 && this.step < this.categories.length - 1) this.nextStep();
          else if (diffX < 0 && this.step > 0) this.prevStep();
          this.touchStartX = 0;
          this.touchStartY = 0; // ← reset
        }
      },


      handleTouchEnd() {
        this.touchStartX = 0;
        this.touchStartY = 0; // ← reset
      },

      sendToWhatsApp() {
  const number = "923032614853";

  let msg = " *Shabbir Hussain Catering* \n";

  msg += `*Thaal Quantity:* ${this.thaalQuantity}\n\n`;
  msg += "*Selections:*\n";
  msg += "*Please provide a quote for this menu:*\n";

  let idx = 1;
  for (const cat in this.selected) {
    msg += `${idx}. *${cat}:* ${this.selected[cat]}\n`;
    idx++;
  }

  msg += `\n🔗 *Website:* https://shabbirhussaincatering.com`;

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
    box-shadow: 0 50px 120px rgba(0, 0, 0, 0.25);
    border: 12px solid #D4AF37;
  }

  .preview-wrapper-mobile {
    width: 280px;
    height: 280px;
    margin: 0 auto;
    position: relative;
    border-radius: 50%;
    overflow: hidden;
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.3);
    background: white;
    /* border: 2px solid #D4AF37; */
  }

  .preview-item,
  .preview-item-mobile {
    position: absolute;
    border-radius: 50%;
    border: 4px solid #D4AF37;
    box-shadow: 0 20px 50px rgba(0, 0, 0, 0.3);
    object-fit: cover;
    transition: all 0.4s ease;
  }

  .preview-item:hover,
  .preview-item-mobile:hover {
    transform: scale(1.4);
    border-color: #8B1E3F;
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.4);
    z-index: 50;
  }

  .royal-card {
    background: rgba(255, 255, 255, 0.94);
    backdrop-filter: blur(20px);
    border: 2px solid #D4AF37;
    border-radius: 36px;
    padding: 48px 32px;
    box-shadow: 0 30px 100px rgba(139, 30, 63, 0.15);
  }

  .group-header {
    grid-column: 1 / -1;
    margin: 40px 0 20px;
    padding: 16px 24px;
    background: #8B1E3F;
    color: white;
    font-size: 16px;
    font-weight: 900;
    text-transform: uppercase;
    letter-spacing: 2px;
    text-align: center;
    border-radius: 12px;
  }

  .royal-item-card {
    background: rgba(255, 255, 255, 0.8);
    border: 2px solid #D4AF37;
    border-radius: 24px;
    padding: 24px 18px;
    text-align: center;
    font-weight: 600;
    font-size: 15px;
    color: #5D4037;
    cursor: pointer;
    transition: all 0.4s ease;
    backdrop-filter: blur(10px);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  }

  .royal-item-card:hover {
    transform: translateY(-10px);
    background: rgba(255, 255, 255, 0.95);
    border-color: #8B1E3F;
  }

  .royal-item-card.selected {
    background: linear-gradient(135deg, #8B1E3F, #A52A2A);
    color: white;
    transform: scale(1.07);
    border-color: #D4AF37;
    box-shadow: 0 0 50px rgba(139, 30, 63, 0.5);
  }

  .gold-check {
    position: absolute;
    top: 12px;
    right: 12px;
    width: 36px;
    height: 36px;
    background: #D4AF37;
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
    border-radius: 100%;
    border: 2px solid rgba(212, 175, 55, 0.8);
    opacity: 0.95;
  }

  .center-logo {
    width: 20%;
    height: 38%;
  }

  .center-logo-mobile {
    width: 30%;
    height: 30%;
  }

  .glass-btn {
    background: rgba(255, 255, 255, 0.3);
    backdrop-filter: blur(16px);
    border: 2px solid #D4AF37;
    color: #5D4037;
    font-weight: 800;
    font-size: 1.1rem;
    padding: 14px 32px;
    border-radius: 20px;
    transition: all 0.4s;
    box-shadow: 0 8px 32px rgba(139, 30, 63, 0.15);
  }

  .glass-btn:hover:not(:disabled) {
    transform: translateY(-6px);
    background: rgba(255, 255, 255, 0.5);
    border-color: #8B1E3F;
    box-shadow: 0 20px 50px rgba(139, 30, 63, 0.25);
  }

  .glass-btn-primary {
    background: linear-gradient(135deg, #8B1E3F, #A52A2A);
    color: white;
    border: 2px solid #D4AF37;
    box-shadow: 0 10px 40px rgba(139, 30, 63, 0.4);
  }

  .glass-btn-primary:hover:not(:disabled) {
    background: linear-gradient(135deg, #A52A2A, #8B1E3F);
    transform: translateY(-8px);
    box-shadow: 0 25px 60px rgba(139, 30, 63, 0.5);
  }

  .quantity-btn {
    min-width: 60px;
    min-height: 60px;
    border-radius: 50%;
    aspect-ratio: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background: white;
    border: 4px solid #D4AF37;
    font-weight: 300;
  }

  @media (max-width: 480px) {
    .quantity-btn {
      min-width: 50px;
      min-height: 50px;
    }
  }

 /* TOAST – Full Width, Royal Gold, Beautiful Animation */
.toast {
  position: fixed;
  left: 50%;
  bottom: 24px;
  transform: translateX(-50%) translateY(100px);
  opacity: 0;
  z-index: 9999;
  width: 92%;
  max-width: 420px;
  padding: 18px 24px;
  border-radius: 28px;
  background: linear-gradient(135deg, #8B1E3F 0%, #A52A2A 100%);
  color: white;
  font-weight: 800;
  font-size: 1.1rem;
  text-align: center;
  box-shadow: 0 20px 50px rgba(139, 30, 63, 0.4);
  border: 3px solid #D4AF37;
  backdrop-filter: blur(12px);
  transition: all 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
  pointer-events: none;
}

.toast.show {
  transform: translateX(-50%) translateY(0);
  opacity: 1;
}

/* Optional: Add a golden shine sweep effect */
.toast::before {
  content: '';
  position: absolute;
  top: 0; left: -100%;
  width: 50%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
  transform: skewX(-25deg);
  transition: none;
  animation: shine 2s infinite;
}

@keyframes shine {
  0% { left: -100%; }
  100% { left: 100%; }
}

hr{
  background:red
  
}
/* Mobile fine-tuning */
@media (max-width: 480px) {
  .toast {
    bottom: 16px;
    width: 94%;
    font-size: 1rem;
    padding: 16px 20px;
  }
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

  .fade-in {
    opacity: 1;
    transition: opacity 0.5s;
  }

  .fade-out {
    opacity: 0;
    transition: opacity 0.3s;
  }

  /* Make input look even more royal */
input::-webkit-inner-spin-button,
input::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
input[type=number] {
  -moz-appearance: textfield;
}

/* Pulse animation when focused */
input:focus + .blur-md {
  animation: pulse-glow 2s infinite;
}

@keyframes pulse-glow {
  0%, 100% { opacity: 0.7; }
  50% { opacity: 1; transform: scaleX(1.05); }
}
</style>