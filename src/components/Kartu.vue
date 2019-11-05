<template>
  <div class="kartu-item" :class="{'-active':kartuFlipped}">
    <div class="kartu-item__side -front">
      <div
        class="kartu-item__focus"
        :class="{'-active' : fokusElStyle }"
        :style="fokusElStyle"
        ref="focusElement"
      ></div>
      <div class="kartu-item__cover">
        <img v-if="gambarKartuSekarang" :src="gambarKartuSekarang" class="kartu-item__bg" />
      </div>
      <div class="kartu-item__wrapper">
        <div class="kartu-item__top">
          <img
            src="https://raw.githubusercontent.com/muhammederdem/credit-card-form/master/src/assets/images/chip.png"
            class="kartu-item__chip"
          />
          <div class="kartu-item__type">
            <transition name="slide-fade-up">
              <img
                :src="'https://raw.githubusercontent.com/muhammederdem/credit-card-form/master/src/assets/images/' + typeKartu + '.png'"
                v-if="typeKartu"
                :key="typeKartu"
                alt
                class="kartu-item__typeImg"
              />
            </transition>
          </div>
        </div>
        <label :for="fields.nomorKartu" class="kartu-item__nomor" :ref="fields.nomorKartu">
          <template>
            <span v-for="(n, $index) in placeholderSekarang" :key="$index">
              <transition name="slide-fade-up">
                <div class="kartu-item__nomorItem" v-if="getNomorKartuMasked($index, n)">*</div>
                <div
                  class="kartu-item__nomorItem"
                  :class="{ '-active' : n.trim() === '' }"
                  :key="placeholderSekarang"
                  v-else-if="labels.nomorKartu.length > $index"
                >{{labels.nomorKartu[$index]}}</div>
                <div
                  class="kartu-item__nomorItem"
                  :class="{ '-active' : n.trim() === '' }"
                  v-else
                  :key="placeholderSekarang + 1"
                >{{n}}</div>
              </transition>
            </span>
          </template>
        </label>
        <div class="kartu-item__content">
          <label :for="fields.namaKartu" class="kartu-item__info" :ref="fields.namaKartu">
            <div class="kartu-item__holder">Card Holder</div>
            <transition name="slide-fade-up">
              <div class="kartu-item__name" v-if="labels.namaKartu.length" key="1">
                <transition-group name="slide-fade-right">
                  <span
                    class="kartu-item__nameItem"
                    v-for="(n, $index) in labels.namaKartu.replace(/\s\s+/g, ' ')"
                    :key="$index + 1"
                  >{{n}}</span>
                </transition-group>
              </div>
              <div class="kartu-item__name" v-else key="2">Nama Panjang</div>
            </transition>
          </label>
          <div class="kartu-item__date" ref="tanggalKartu">
            <label :for="fields.bulanKartu" class="kartu-item__dateTitle">Kadaluarsa</label>
            <label :for="fields.bulanKartu" class="kartu-item__dateItem">
              <transition name="slide-fade-up">
                <span v-if="labels.bulanKartu" :key="labels.bulanKartu">{{labels.bulanKartu}}</span>
                <span v-else key="2">MM</span>
              </transition>
            </label>
            /
            <label for="tahunKartu" class="kartu-item__dateItem">
              <transition name="slide-fade-up">
                <span
                  v-if="labels.tahunKartu"
                  :key="labels.tahunKartu"
                >{{String(labels.tahunKartu).slice(2,4)}}</span>
                <span v-else key="2">YY</span>
              </transition>
            </label>
          </div>
        </div>
      </div>
    </div>
    <div class="kartu-item__side -back">
      <div class="kartu-item__cover">
        <img v-if="gambarKartuSekarang" :src="gambarKartuSekarang" class="kartu-item__bg" />
      </div>
      <div class="kartu-item__band"></div>
      <div class="kartu-item__cvv">
        <div class="kartu-item__cvvTitle">CVV</div>
        <div class="kartu-item__cvvBand">
          <span v-for="(n, $index) in labels.cvvKartu" :key="$index">*</span>
        </div>
        <div class="kartu-item__type">
          <img
            :src="'https://raw.githubusercontent.com/muhammederdem/credit-card-form/master/src/assets/images/' + typeKartu + '.png'"
            v-if="typeKartu"
            class="kartu-item__typeImg"
          />
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "Kartu",
  props: {
    labels: Object,
    fields: Object,
    nomorKartuMasked: Boolean,
    acakGambar: {
      type: Boolean,
      default: true
    },
    gambarKartu: [String, Object]
  },
  data() {
    return {
      fokusElStyle: null,
      fokusSekarang: null,
      fokus: false,
      kartuFlipped: false,
      placeholderKartuAmex: "#### ###### #####",
      placeholderKartuDinners: "#### ###### ####",
      placeholderDefaultKartu: "#### #### #### ####",
      placeholderSekarang: ""
    };
  },
  watch: {
    fokusSekarang() {
      if (this.fokusSekarang) {
        this.ubahFokus();
      } else {
        this.fokusElStyle = null;
      }
    },
    typeKartu() {
      this.ubahPlaceholder();
    }
  },
  mounted() {
    this.ubahPlaceholder();

    let ini = this;
    let fields = document.querySelectorAll("[data-kartu-field]");
    fields.forEach(el => {
      el.addEventListener("focus", () => {
        this.fokus = true;
        if (
          el.id === this.fields.tahunKartu ||
          el.id === this.fields.bulanKartu
        ) {
          this.fokusSekarang = "tanggalKartu";
        } else {
          this.fokusSekarang = el.id;
        }
        this.kartuFlipped = el.id === this.fields.cvvKartu;
      });
      el.addEventListener("blur", () => {
        this.kartuFlipped = !el.id === this.fields.cvvKartu;
        setTimeout(() => {
          if (!ini.fokus) {
            ini.fokusSekarang = null;
          }
        }, 300);
        ini.fokus = false;
      });
    });
  },
  computed: {
    typeKartu() {
      //mencari type kartu apabilsa sama dengan persyaratan maka typenya adalah yang di return
      let nomor = this.labels.nomorKartu;
      let re = new RegExp("^4");
      if (nomor.match(re) != null) return "visa";

      re = new RegExp("^(34|37)");
      if (nomor.match(re) != null) return "amex";

      re = new RegExp("^5[1-5]");
      if (nomor.match(re) != null) return "mastercard";

      re = new RegExp("^6011");
      if (nomor.match(re) != null) return "discover";

      re = new RegExp("^62");
      if (nomor.match(re) != null) return "unionpay";

      re = new RegExp("^9792");
      if (nomor.match(re) != null) return "troy";

      re = new RegExp("^3(?:0([0-5]|9)|[689]\\d?)\\d{0,11}");
      if (nomor.match(re) != null) return "dinersclub";

      re = new RegExp("^35(2[89]|[3-8])");
      if (nomor.match(re) != null) return "jcb";

      return ""; // default type
    },
    gambarKartuSekarang() {
      //background yang ada sekarang
      //acak angka untuk mendapatkan gambar
      if (this.acakGambar && !this.gambarKartu) {
        // TODO will be optimized
        let acak = Math.floor(Math.random() * 25 + 1);
        return `https://raw.githubusercontent.com/muhammederdem/credit-card-form/master/src/assets/images/${acak}.jpeg`;
      } else if (this.gambarKartu) {
        return this.gambarKartu;
      } else {
        return null;
      }
    }
  },
  methods: {
    ubahFokus() {
      let target = this.$refs[this.fokusSekarang];
      this.fokusElStyle = target
        ? {
            width: `${target.offsetWidth}px`,
            height: `${target.offsetHeight}px`,
            transform: `translateX(${target.offsetLeft}px) translateY(${target.offsetTop}px)`
          }
        : null;
    },
    getNomorKartuMasked(index, n) {
      return (
        index > 4 &&
        index < 14 &&
        this.labels.nomorKartu.length > index &&
        n.trim() !== "" &&
        this.nomorKartuMasked
      );
    },
    ubahPlaceholder() {
      if (this.typeKartu === "amex") {
        this.placeholderSekarang = this.placeholderKartuAmex;
      } else if (this.cardType === "dinersclub") {
        this.placeholderSekarang = this.placeholderKartuDinners;
      } else {
        this.placeholderSekarang = this.placeholderDefaultKartu;
      }
      this.$nextTick(() => {
        this.ubahFokus();
      });
    }
  }
};
</script>