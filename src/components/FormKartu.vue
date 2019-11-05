<template>
  <div class="kartu-form">
    <div class="kartu-list">
      <Kartu
        :fields="fields"
        :labels="formData"
        :nomorKartuMasked="nomorKartuMasked"
        :gambarKartu="gambarKartu"
        :acakGambar="acakGambar"
      />
    </div>
    <div class="kartu-form__inner">
      <div class="kartu-input">
        <label for="nomorKartu" class="kartu-input__label">Nomor Kartu</label>
        <input
          type="tel"
          :id="fields.nomorKartu"
          @input="ubahNomor"
          @focus="fokusNomorKartu"
          @blur="blurNomorKartu"
          :value="formData.nomorKartu"
          :maxlength="panjangNomorKartu"
          class="kartu-input__input"
          data-kartu-field
          autocomplete="off"
        />
        <button
          class="kartu-input__eye"
          :class="{ '-active' : !nomorKartuMasked }"
          title="Show/Hide kartu number"
          tabindex="-1"
          :disabled="formData.nomorKartu === ''"
          @click="toggleMask"
        ></button>
      </div>
      <div class="kartu-input">
        <label for="namaKartu" class="kartu-input__label">Nama</label>
        <input
          type="text"
          :id="fields.namaKartu"
          v-letter-only
          @input="ubahNama"
          class="kartu-input__input"
          :value="formData.namaKartu"
          data-kartu-field
          autocomplete="off"
        />
      </div>
      <div class="kartu-form__row">
        <div class="kartu-form__col">
          <div class="kartu-from__group">
            <label for="tanggalKadaluarsa" class="kartu-input__label">Tanggal Kadaluarsa</label>
            <select
              class="kartu-input__input -select"
              :id="fields.bulanKartu"
              v-model="formData.bulanKartu"
              @change="ubahBulan"
              data-kartu-field
            >
              <option value disabled selected>Bulan</option>
              <option
                v-bind:value="n < 10 ? '0' + n : n"
                v-for="n in 12"
                v-bind:disabled="n < minBulanKartu"
                v-bind:key="n"
              >{{generateValueBulan(n)}}</option>
            </select>
            <select
              class="kartu-input__input -select"
              :id="fields.tahunKartu"
              v-model="formData.tahunKartu"
              @change="ubahTahun"
              data-kartu-field
            >
              <option value disabled selected>Tahun</option>
              <option
                v-bind:value="$index + minTahunKartu"
                v-for="(n, $index) in 12"
                v-bind:key="n"
              >{{$index + minTahunKartu}}</option>
            </select>
          </div>
        </div>
        <div class="kartu-form__col -cvv">
          <div class="kartu-input">
            <label for="cvvKartu" class="kartu-input__label">CW</label>
            <input
              type="tel"
              class="kartu-input__input"
              v-hanya-nomor
              :id="fields.cvvKartu"
              maxlength="4"
              :value="formData.cvvKartu"
              @input="ubahCvv"
              data-kartu-field
              autocomplete="off"
            />
          </div>
        </div>
      </div>
      <button class="kartu-form__button">Submit</button>
    </div>
  </div>
</template>
<script>
import Kartu from "./Kartu";
export default {
  name: "FormKartu",
  directives: {
    "hanya-nomor": {
      bind(el) {
        function checkValue(event) {
          event.target.value = event.target.value.replace(/[^0-9]/g, "");
          if (event.charCode >= 48 && event.charCode <= 57) {
            return true;
          }
          event.preventDefault();
        }
        el.addEventListener("keypress", checkValue);
      }
    },
    "letter-only": {
      bind(el) {
        function checkValue(event) {
          if (event.charCode >= 48 && event.charCode <= 57) {
            event.preventDefault();
          }
          return true;
        }
        el.addEventListener("keypress", checkValue);
      }
    }
  },
  components: {
    Kartu
  },
  props: {
    formData: {
      type: Object,
      default: () => {
        return {
          namaKartu: "",
          nomorKartu: "",
          bulanKartu: "",
          tahunKartu: "",
          cvvKartu: ""
        };
      }
    },
    gambarKartu: [String, Object],
    acakGambar: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      fields: {
        nomorKartu: "v-nomor-kartu",
        namaKartu: "v-nama-kartu",
        bulanKartu: "v-bulan-kartu",
        tahunKartu: "v-tahun-kartu",
        cvvKartu: "v-cvv-kartu"
      },
      minTahunKartu: new Date().getFullYear(),
      panjangNomorKartu: 19,
      nomorKartuMasked: true,
      mainNomorKartu: this.nomorKartu
    };
  },
  computed: {
    minBulanKartu() {
      if (this.formData.tahunKartu === this.minTahunKartu)
        return new Date().getMonth() + 1;
      return 1;
    }
  },
  watch: {
    tahunKartu() {
      if (this.formData.bulanKartu < this.minBulanKartu) {
        this.formData.bulanKartu = "";
      }
    }
  },
  mounted() {
    this.maskNomorKartu();
  },
  methods: {
    kartuTidakValid() {
      let nomor = this.formData.nomorKartu;
      let jmlh = 0;
      let kondisi = true;
      for (let i = nomor.length - 1; i >= 0; i--) {
        let nom = nomor.charAt(i);
        if (kondisi) {
          jmlh += nom;
        } else {
          nom = nom * 2;
          if (nom > 9) {
            nom = nom
              .toString()
              .split("")
              .join("+");
          }
          jmlh += nom;
        }
        kondisi = !kondisi;
      }
      if (jmlh % 10 !== 0) {
        alert("kartu tidak valid");
      }
    },
    //--------------function option bulan-------------------------//

    generateValueBulan(n) {
      return n < 10 ? `0${n}` : n;
    },

    ubahBulan() {
      this.$emit("input-bulan-kartu", this.formData.bulanKartu);
    },

    //--------------function input tahun-------------------------//
    ubahTahun() {
      this.$emit("input-tahun-kartu", this.formData.tahunKartu);
    },
    //--------------function input cvv-------------------------//
    ubahCvv(e) {
      this.formData.cvvKartu = e.target.value;
      this.$emit("input-cvv-kartu", this.formData.cvvKartu);
    },
    //--------------function input nama-------------------------//

    ubahNama(e) {
      this.formData.namaKartu = e.target.value;
      this.$emit("input-nama-kartu", this.formData.namaKartu);
    },

    //--------------function input nomor-----------------------//

    ubahNomor(e) {
      this.formData.nomorKartu = e.target.value;
      let value = this.formData.nomorKartu.replace(/\D/g, "");
      // console.log(value)
      //handle inputan
      if (/^3[47]\d{0,13}$/.test(value)) {
        this.formData.nomorKartu = value
          .replace(/(\d{4})/, "$1 ")
          .replace(/(\d{4}) (\d{6})/, "$1 $2 ");
        this.panjangNomorKartu = 17;
      } else if (/^3(?:0[0-5]|[68]\d)\d{0,11}$/.test(value)) {
        // diner's club, 14 digits
        this.formData.nomorKartu = value
          .replace(/(\d{4})/, "$1 ")
          .replace(/(\d{4}) (\d{6})/, "$1 $2 ");
        this.panjangNomorKartu = 16;
      } else if (/^\d{0,16}$/.test(value)) {
        // regular cc number, 16 digits
        this.formData.nomorKartu = value
          .replace(/(\d{4})/, "$1 ")
          .replace(/(\d{4}) (\d{4})/, "$1 $2 ")
          .replace(/(\d{4}) (\d{4}) (\d{4})/, "$1 $2 $3 ");
        this.panjangNomorKartu = 19;
      }
      this.$emit("input-card-number", this.formData.nomorKartu);
    },

    fokusNomorKartu() {
      this.unMaskNomorKartu();
    },

    unMaskNomorKartu() {
      this.formData.nomorKartu = this.mainNomorKartu;
    },

    blurNomorKartu() {
      if (this.nomorKartuMasked) {
        this.maskNomorKartu();
      }
    },

    maskNomorKartu() {
      this.mainNomorKartu = this.formData.nomorKartu;
      let arr = this.formData.nomorKartu.split("");
      arr.forEach((element, index) => {
        if (index > 4 && index < 14 && element.trim() !== "") {
          arr[index] = "*";
        }
      });
      this.formData.nomorKartu = arr.join("");
    },

    toggleMask() {
      this.nomorKartuMasked = !this.nomorKartuMasked;
      if (this.nomorKartuMasked) {
        this.maskNomorKartu();
      } else {
        this.unMaskNomorKartu();
      }
    }
  }
};
</script>