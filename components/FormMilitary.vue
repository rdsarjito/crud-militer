<template>
    <div>
      <div v-if="isLoading">
        <Loading :title="isLoadingTitle" />
      </div>
      <form @submit.prevent="submitForm" action="" enctype="multipart/form-data">
        <div class="mb-3">
          <label>Nama</label>
          <input type="text" v-model="military.nama" class="form-control" />
          <span class="text-danger" v-if="errorList.nama && !savedSuccessfully">{{ errorList.nama[0] }}</span>
        </div>
        <div class="mb-3">
          <label>Jenis</label>
          <select v-model="military.jenis" class="form-control">
            <option value="Senjata" >Senjata</option>
            <option value="Kendaraan">Kendaraan</option>
            <option value="Pesawat">Pesawat</option>
          </select>
          <span class="text-danger" v-if="errorList.jenis && !savedSuccessfully">{{ errorList.jenis[0] }}</span>
        </div>
        <div class="mb-3">
          <label>Kondisi</label>
          <select v-model="military.kondisi" class="form-control">
            <option value="Baru">Baru</option>
            <option value="Bekas">Bekas</option>
            <option value="Rusak">Rusak</option>
          </select>
          <span class="text-danger" v-if="errorList.kondisi && !savedSuccessfully">{{ errorList.kondisi[0] }}</span>
        </div>
        <div class="mb-3">
          <label>Tahun Produksi</label>
          <input type="date" v-model="military.tahun_produksi" class="form-control" />
          <span class="text-danger" v-if="errorList.tahun_produksi && !savedSuccessfully">{{ errorList.tahun_produksi[0] }}</span>
        </div>
        <div class="mb-3">
          <label>Tanggal Perolehan</label>
          <input type="date" v-model="military.tanggal_perolehan" class="form-control" />
          <span class="text-danger" v-if="errorList.tanggal_perolehan && !savedSuccessfully">{{ errorList.tanggal_perolehan[0] }}</span>
        </div>
        <div class="mb-3">
          <label>Gambar</label>
          <input type="file" @change="onFileChange" class="form-control" accept="image/*" />
          <span class="text-danger" v-if="errorList.gambar && !savedSuccessfully">{{ errorList.gambar[0] }}</span>
        </div>
        <div class="mb-3">
          <label>Matra</label>
          <select v-model="military.matra" class="form-control">
            <option value="TNI-AU">TNI-AU</option>
            <option value="TNI-AD">TNI-AD</option>
            <option value="TNI-AL">TNI-AL</option>
            <option value="KEMHAN">KEMHAN</option>
          </select>
          <span class="text-danger" v-if="errorList.matra && !savedSuccessfully">{{ errorList.matra[0] }}</span>
        </div>
        <div class="mb-3">
          <button type="submit" class="btn btn-primary">{{ buttonText }}</button>
        </div>
      </form>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      militaryData: {
        type: Object,
        required: true
      },
      errorListData: {
        type: Object,
        required: true
      },
      isLoading: {
        type: Boolean,
        default: false
      },
      isLoadingTitle: {
        type: String,
        default: "Loading"
      },
      buttonText: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        military: { ...this.militaryData },
        errorList: { ...this.errorListData},
        savedSuccessfully: false
      }
    },
    watch: {
      militaryData: {
        handler(newVal) {
          this.military = { ...newVal };
        },
        deep: true
      },
      errorListData: {
        handler(newVal) {
          this.errorList = { ...newVal };
        },
        deep: true
      }
    },
    methods: {
      submitForm() {
        this.$emit('form-submitted', this.military);
      },
      onFileChange(e) {
        const file = e.target.files[0];
        this.military.gambar = file;
      }
    }
  }
  </script>