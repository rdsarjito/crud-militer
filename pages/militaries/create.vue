<template>
    <div class="mt-5 container">
        <div class="card">
            <div class="card-header">
                <h4>Tambah Military
                    <NuxtLink to="/militaries" class="btn btn-danger float-end">Back</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <FormMilitary :militaryData="military" :errorListData="errorList" :isLoading="isLoading" :isLoadingTitle="isLoadingTitle" @form-submitted="handleFormSubmitted" buttonText="Save" />
            </div>
        </div>
    </div>
</template>
  
<script>
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import FormMilitary from '../../components/FormMilitary.vue';

    export default {
      name: "militaryCreate",
      components: {
        FormMilitary
      },
      data() {
        return {
            military: {
                nama: '',
                jenis: '',
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
        handleFormSubmitted(militaryData) {
            this.military = militaryData;
            this.saveMilitary();
        },
        saveMilitary() {
            let myThis = this;
            this.isLoading = true;
            this.isLoadingTitle = "Saving";
            let formData = new FormData();
            formData.append('gambar', this.military.gambar);
            formData.append('nama', this.military.nama);
            formData.append('jenis', this.military.jenis);
            formData.append('kondisi', this.military.kondisi);
            formData.append('tahun_produksi', this.military.tahun_produksi);
            formData.append('tanggal_perolehan', this.military.tanggal_perolehan);
            formData.append('matra', this.military.matra);

            axios.post('http://localhost:8000/api/militaries', formData).then(res => { 
                this.military.nama = '';
                this.military.jenis = '';
                this.military.kondisi = '';
                this.military.tahun_produksi = '';
                this.military.tanggal_perolehan = '';
                this.military.matra = '';
                this.military.gambar = '';
                this.errorList = {};  
                this.savedSuccessfully = true;
                this.isLoading = false;

                Swal.fire({
                    title: "Selamat!",
                    text: "Tambah Data Berhasil!",
                    icon: "success"
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