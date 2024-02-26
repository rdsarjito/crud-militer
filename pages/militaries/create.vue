<template>
    <div class="mt-5 container">
      <div class="card">
        <div class="card-header">
          <h4>Tambah Military
            <NuxtLink to="/" class="btn btn-danger float-end">Back</NuxtLink>
          </h4>
        </div>
        <div class="card-body">
          <form @submit.prevent="saveMilitary" action="">
            <div class="mb-3">
              <label>Nama</label>
              <input type="text" v-model="military.nama" class="form-control" />
            </div>
            <div class="mb-3">
              <label>Jenis</label>
              <select v-model="military.jenis" class="form-control">
                <option value="senjata">Senjata</option>
                <option value="kendaraan">Kendaraan</option>
                <option value="pesawat">Pesawat</option>
              </select>
            </div>
            <div class="mb-3">
              <label>Kondisi</label>
              <input type="text" v-model="military.kondisi" class="form-control" />
            </div>
            <div class="mb-3">
              <label>Tahun Produksi</label>
              <input type="text" v-model="military.tahun_produksi" class="form-control" />
            </div>
            <div class="mb-3">
              <label>Tanggal Perolehan</label>
              <input type="text" v-model="military.tanggal_perolehan" class="form-control" />
            </div>
            <div class="mb-3">
              <label>Gambar</label>
              <input type="text" v-model="military.gambar" class="form-control" />
            </div>
            <div class="mb-3">
              <label>Matra</label>
              <select v-model="military.matra" class="form-control">
                <option value="tni-au">TNI AU</option>
                <option value="tni-ad">TNI AD</option>
                <option value="tni-al">TNI AL</option>
                <option value="kemhan">KEMHAN</option>
              </select>
            </div>
            <div class="mb-3">
              <button type="submit" class="btn btn-primary">Save</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  <script>
    export default {
      name: "militaryCreate",
      data() {
        return {
          military: {
            nama: '',
            jenis: '',
            type: '',
            kondisi: '',
            tahun_produksi: '',
            tanggal_perolehan: '',
            matra: '',
            gambar: ''
          }
        }
      },
      methods: {
        saveMilitary() {
          console.log("Hiiii")
        }
      }
    }
  </script>