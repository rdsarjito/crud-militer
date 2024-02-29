<template>
    <div class="mt-5 container">
        <div class="card">
            <div class="card-header">
                <h4>Tambah Military
                    <NuxtLink to="/militaries" class="btn btn-danger float-end">Back</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <div v-if="isLoading">
                    <Loading :title="isLoadingTitle" />
                </div>
                <form @submit.prevent="saveMilitary" action="" enctype="multipart/form-data">
                    <div class="mb-3">
                        <label>Nama</label>
                        <input type="text" v-model="military.nama" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.nama && !savedSuccessfully">{{ this.errorList.nama[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Jenis</label>
                        <select v-model="military.jenis" class="form-control">
                            <option value="senjata">Senjata</option>
                            <option value="kendaraan">Kendaraan</option>
                            <option value="pesawat">Pesawat</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.jenis && !savedSuccessfully">{{ this.errorList.jenis[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Type</label>
                        <input type="text" v-model="military.type" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.type && !savedSuccessfully">{{ this.errorList.type[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Kondisi</label>
                        <select v-model="military.kondisi" class="form-control">
                            <option value="senjata">Baru</option>
                            <option value="kendaraan">Bekas</option>
                            <option value="pesawat">Rusak</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.kondisi && !savedSuccessfully">{{ this.errorList.kondisi[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Tahun Produksi</label>
                        <input type="text" v-model="military.tahun_produksi" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.tahun_produksi && !savedSuccessfully">{{ this.errorList.tahun_produksi[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Tanggal Perolehan</label>
                        <input type="text" v-model="military.tanggal_perolehan" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.tanggal_perolehan && !savedSuccessfully">{{ this.errorList.tanggal_perolehan[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Gambar</label>
                        <input type="file" @change="onFileChange" class="form-control" accept="image/*" />
                        <span class="text-danger" v-if="this.errorList.gambar && !savedSuccessfully">{{ this.errorList.gambar[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Matra</label>
                        <select v-model="military.matra" class="form-control">
                            <option value="tni-au">TNI AU</option>
                            <option value="tni-ad">TNI AD</option>
                            <option value="tni-al">TNI AL</option>
                            <option value="kemhan">KEMHAN</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.matra && !savedSuccessfully">{{ this.errorList.matra[0] }}</span>
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
                gambar: null
            },
            isLoading: false,
            isLoadingTitle: 'Loading',
            errorList: {},
            savedSuccessfully: false
        }
      },
      methods: {
        saveMilitary() {
            let myThis = this;
            this.isLoading = true;
            this.isLoadingTitle = "Saving";
            let formData = new FormData();
            console.log(this.military.matra);
            formData.append('gambar', this.military.gambar);
            formData.append('nama', this.military.nama);
            formData.append('jenis', this.military.jenis);
            formData.append('type', this.military.type);
            formData.append('kondisi', this.military.kondisi);
            formData.append('tahun_produksi', this.military.tahun_produksi);
            formData.append('tanggal_perolehan', this.military.tanggal_perolehan);
            formData.append('matra', this.military.matra);

            axios.post('http://localhost:8000/api/militaries', formData).then(res => {
                console.log(res, 'res');

                this.military.nama = '';
                this.military.jenis = '';
                this.military.type = '';
                this.military.kondisi = '';
                this.military.tahun_produksi = '';
                this.military.tanggal_perolehan = '';
                this.military.matra = '';
                this.military.gambar = '';
                this.errorList = {};  
                this.savedSuccessfully = true;
                this.isLoading = false;
            })
            .catch(function (error) {
                console.log(error, 'errors')
                if(error.response) {
                    if(error.response.status = 422) {
                        myThis.errorList = error.response.data.errors;
                    }
                }
            })
        },
        onFileChange(e) {
            const file = e.target.files[0];
            this.military.gambar = file;
        }
      }
    }
</script>