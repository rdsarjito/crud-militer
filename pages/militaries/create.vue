<template>
    <div class="mt-5 container">
        <div class="card">
            <div class="card-header">
                <h4>Tambah Military
                    <NuxtLink to="/militaries" class="btn btn-danger float-end">Back</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <form @submit.prevent="saveMilitary" action="">
                    <div class="mb-3">
                        <label>Nama</label>
                        <input type="text" v-model="military.nama" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.nama">{{ this.errorList.nama[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Jenis</label>
                        <select v-model="military.jenis" class="form-control">
                            <option value="senjata">Senjata</option>
                            <option value="kendaraan">Kendaraan</option>
                            <option value="pesawat">Pesawat</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.jenis">{{ this.errorList.jenis[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Type</label>
                        <input type="text" v-model="military.type" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.type">{{ this.errorList.type[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Kondisi</label>
                        <input type="text" v-model="military.kondisi" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.kondisi">{{ this.errorList.kondisi[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Tahun Produksi</label>
                        <input type="text" v-model="military.tahun_produksi" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.tahun_produksi">{{ this.errorList.tahun_produksi[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Tanggal Perolehan</label>
                        <input type="text" v-model="military.tanggal_perolehan" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.tanggal_perolehan">{{ this.errorList.tanggal_perolehan[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Gambar</label>
                        <input type="text" v-model="military.gambar" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.gambar">{{ this.errorList.gambar[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Matra</label>
                        <select v-model="military.matra" class="form-control">
                            <option value="tni-au">TNI AU</option>
                            <option value="tni-ad">TNI AD</option>
                            <option value="tni-al">TNI AL</option>
                            <option value="kemhan">KEMHAN</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.matra">{{ this.errorList.matra[0] }}</span>
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
    import axios from 'axios';

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
            },
            errorList: {}
        }
      },
      methods: {
        saveMilitary() {
            let myThis = this;
            axios.post('http://localhost:8000/api/militaries', this.military).then(res => {
                console.log(res, 'res');

                this.military.nama = '';
                this.military.jenis = '';
                this.military.type = '';
                this.military.kondisi = '';
                this.military.tahun_produksi = '';
                this.military.tanggal_perolehan = '';
                this.military.matra = '';
                this.military.gambar = '';
            })
            .catch(function (error) {
                console.log(error, 'errors')
                if(error.response) {
                    if(error.response.status = 422) {
                        myThis.errorList = error.response.data.errors;
                    }
                }
            })
        }
      }
    }
</script>