<template>
    <div>
        <div class="card">
            <div class="card-header">
                <h4>Data Militer
                    <NuxtLink to="/militaries/create" class="btn btn-primary float-end">Tambah Data</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <div class="btn-group">
                    <button class="btn btn-secondary dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">
                        Filter Kondisi
                    </button>
                    <ul class="dropdown-menu">
                        <li><a @click="filterByCondition('Bekas')" class="dropdown-item" href="#">Bekas</a></li>
                        <li><a @click="filterByCondition('Baru')" class="dropdown-item" href="#">Baru</a></li>
                        <li><a @click="filterByCondition('Rusak')" class="dropdown-item" href="#">Rusak</a></li>
                    </ul>
                </div>
                <table class="table table-striped table-bordered">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Nama</th>
                            <th>Jenis</th>
                            <th>Type</th>
                            <th>Kondisi</th>
                            <th>Tahun Produksi</th>
                            <th>Tanggal Perolehan</th>
                            <th>Matra</th>
                            <th>Gambar</th>
                            <th>Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(military, index) in filteredMilitaries" :key="index">
                            <td>{{ military.id }}</td>
                            <td>{{ military.nama }}</td>
                            <td>{{ military.jenis }}</td>
                            <td>{{ military.type }}</td>
                            <td>{{ military.kondisi }}</td>
                            <td>{{ military.tahun_produksi }}</td>
                            <td>{{ military.tanggal_perolehan }}</td>
                            <td>{{ military.matra }}</td>
                            <td>{{ military.gambar }}</td>
                            <td>
                                <NuxtLink :to="`/militaries/${military.id}`" class="btn btn-success btn-sm mx-2">Ubah</NuxtLink>
                                <button type="button" @click="$event => deleteMilitary(military.id)" class="btn btn-danger btn-sm mx-2">Hapus</button>
                            </td>

                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    import Swal from 'sweetalert2';

    export default {
        name: "militaris",
        data() {
            return {
                militaries: [],
                filteredMilitaries: [],
                selectedCondition: null
            }
        },
        mounted() {
            this.getMilitaries();
        },
        methods: {
            getMilitaries() {
                axios.get(`http://localhost:8000/api/militaries`).then(res => {
                    this.militaries = res.data.militaries;
                    this.filteredMilitaries = this.militaries;
                });
            },
            filterByCondition(condition) {
                this.selectedCondition = condition;
                
                if (condition === 'Bekas') {
                    this.filteredMilitaries = this.militaries.filter(military => military.kondisi === 'Bekas');
                    console.log(this.filteredMilitaries)
                } else if (condition === 'Baru') {
                    this.filteredMilitaries = this.militaries.filter(military => military.kondisi === 'Baru');
                } else if (condition === 'Rusak') {
                    this.filteredMilitaries = this.militaries.filter(military => military.kondisi === 'Rusak');
                }
            },
            deleteMilitary(militaryId) {
                axios.delete(`http://localhost:8000/api/militaries/${militaryId}/delete`).then(res => {
                    this.getMilitaries();
                });
                Swal.fire({
                    title: "Selamat!",
                    text: "Hapus Data Berhasil!",
                    icon: "success"
                }).then(() => {
                    window.location.reload(true);
                });
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>