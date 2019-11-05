<template>
  <div class="wrapper" id="app">
    <FormKartu
      :form-data="formData"
      @input-nomor-kartu="updateNomorKartu"
      @input-nama-kartu="updateNamaKartu"
      @input-bulan-kartu="updateBulanKartu"
      @input-tahun-kartu="updateTahunKartu"
      @input-cvv-kartu="updateKartuCvv"
    />
    <footer>
      &copy; Copyright Adhitya Rachman H
    </footer>
  </div>
</template>

<script>
import FormKartu from "./components/FormKartu";
export default {
  name: "app",
  components: {
    FormKartu
  },
  data() {
    return {
      formData: {
        namaKartu: "",
        nomorKartu: "",
        bulanKartu: "",
        tahunKartu: "",
        cvvKartu: ""
      },
     
    };
  },
   methods: {
        updateNomorKartu(val) {},
        updateNamaKartu(val) {},
        updateBulanKartu(val) {},
        updateTahunKartu(val) {},
        updateKartuCvv(val) {}
      }
};
</script>
<style lang="scss">
@import "./assets/styles/main.scss";
</style>