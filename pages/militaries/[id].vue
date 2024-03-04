<template> 
    <div class="mt-5 container">
        <div class="card">
            <div class="card-header">
                <h4>Ubah Military
                    <NuxtLink to="/militaries" class="btn btn-danger float-end">Back</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <div v-if="isLoading">
                    <Loading :title="isLoadingTitle" />
                </div>
                <form @submit.prevent="updateMilitary" action="">
                    <div class="mb-3">
                        <label>Nama</label>
                        <input type="text" v-model="military.nama" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.nama && !savedSuccessfully">{{ this.errorList.nama[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Jenis</label>
                        <select class="form-control">
                            <option :value="'senjata'" :selected="military.jenis === 'senjata'">Senjata</option>
                            <option :value="'kendaraan'" :selected="military.jenis === 'kendaraan'">Kendaraan</option>
                            <option :value="'pesawat'" :selected="military.jenis === 'pesawat'">Pesawat</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.jenis && !savedSuccessfully">{{ this.errorList.jenis[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Type</label>
                        <input type="text" v-model="military.type" class="form-control" />
                        <span class="text-danger" v-if="this.errorList.type">{{ this.errorList.type[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <label>Kondisi</label>
                        <select class="form-control">
                            <option :value="'senjata'" :selected="military.kondisi === 'senjata'">Baru</option>
                            <option :value="'kendaraan'" :selected="military.kondisi === 'kendaraan'">Bekas</option>
                            <option :value="'pesawat'" :selected="military.kondisi === 'pesawat'">Rusak</option>
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
                        <select class="form-control">
                            <option :value="'tni-au'" :selected="military.matra === 'tni-au'">TNI AU</option>
                            <option :value="'tni-ad'" :selected="military.matra === 'tni-ad'">TNI AD</option>
                            <option :value="'tni-al'" :selected="military.matra === 'tni-al'">TNI AL</option>
                            <option :value="'kemhan'" :selected="military.matra === 'kemhan'">KEMHAN</option>
                        </select>
                        <span class="text-danger" v-if="this.errorList.matra && !savedSuccessfully">{{ this.errorList.matra[0] }}</span>
                    </div>
                    <div class="mb-3">
                        <button type="submit" class="btn btn-primary">Update</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    import Swal from 'sweetalert2';

    export default {
      name: "militaryEdit",
      data() {
        return {
            militaryId: '',
            military: {},
            errorList: {}
        }
      },
      mounted() {
        this.militaryId = this.$route.params.id;
        this.getMilitary(this.militaryId);
      },
      methods: {
        getMilitary(militaryId) {
            axios.get(`http://localhost:8000/api/militaries/${militaryId}`).then(res => {
                this.military = res.data.militaries;
            })
        },
        updateMilitary() {
            let myThis = this;
            this.isLoading = true;
            this.isLoadingTitle = "Updating";
            let formData = new FormData();
            
            if (this.military.gambar) {
                
            }
            formData.append('gambar', this.military.gambar);
            formData.append('nama', this.military.nama);
            formData.append('jenis', this.military.jenis);
            formData.append('type', this.military.type);
            formData.append('kondisi', this.military.kondisi);
            formData.append('tahun_produksi', this.military.tahun_produksi);
            formData.append('tanggal_perolehan', this.military.tanggal_perolehan);
            formData.append('matra', this.military.matra);

            axios.post(`http://localhost:8000/api/militaries/${this.militaryId}/update`, formData).then(res => {
                this.errorList = {};  
                this.savedSuccessfully = true;
                this.isLoading = false;

                myThis.military.nama = '';
                    myThis.military.jenis = '';
                    myThis.military.type = '';
                    myThis.military.kondisi = '';
                    myThis.military.tahun_produksi = '';
                    myThis.military.tanggal_perolehan = '';
                    myThis.military.matra = '';
                    myThis.military.gambar = '';

                    Swal.fire({
                        title: "Selamat!",
                        text: "Ubah Data Berhasil!",
                        icon: "success"
                    }).then(() => {
                        myThis.$router.push('/militaries');
                    });
            })
            .catch(function (error) {
                console.log(error, 'errors')
                if(error.response) {
                    if(error.response.status == 422) {
                        myThis.errorList = error.response.data.errors;
                        myThis.isLoading = false;
                    }
                }
                Swal.fire({
                    icon: "error",
                    title: "Maaf...",
                    text: "Ada Kesalahan!"
                });
            })
        },
        onFileChange(e) {
            const file = e.target.files[0];
            this.military.gambar = file;
        }
      }
    }
</script>