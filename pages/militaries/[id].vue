<template> 
    <div class="mt-5 container">
        <div class="card">
            <div class="card-header">
                <h4>Ubah Military
                    <NuxtLink to="/militaries" class="btn btn-danger float-end">Back</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <FormMilitary :militaryData="military" :isLoading="isLoading" :isLoadingTitle="isLoadingTitle" @form-submitted="handleFormSubmitted" buttonText="Ubah" />
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import FormMilitary from '../../components/FormMilitary.vue';

    export default {
        name: "militaryEdit",
        components: {
            FormMilitary
        },
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
            handleFormSubmitted(militaryData) {
                this.military = militaryData;
                this.updateMilitary();
            },
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
                
                formData.append('gambar', this.military.gambar);
                formData.append('nama', this.military.nama);
                formData.append('jenis', this.military.jenis);
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