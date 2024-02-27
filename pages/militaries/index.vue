<template>
    <div>
        <div class="card">
            <div class="card-header">
                <h4>Data Militer
                    <NuxtLink to="/militaries/create" class="btn btn-primary float-end">Tambah Data</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
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
                        <tr v-for="(militaries, index) in militaries" :key="index">
                            <td>{{ militaries.id }}</td>
                            <td>{{ militaries.nama }}</td>
                            <td>{{ militaries.jenis }}</td>
                            <td>{{ militaries.type }}</td>
                            <td>{{ militaries.kondisi }}</td>
                            <td>{{ militaries.tahun_produksi }}</td>
                            <td>{{ militaries.tanggal_perolehan }}</td>
                            <td>{{ militaries.matra }}</td>
                            <td>{{ militaries.gambar }}</td>
                            <td>
                                <NuxtLink :to="`/militaries/${militaries.id}`" class="btn btn-success btn-sm mx-2">Ubah</NuxtLink>
                                <button type="button" class="btn btn-danger btn-sm mx-2">Hapus</button>
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

    export default {
        name: "militaris",
        data() {
            return {
                militaries: {},
            }
        },
        mounted() {
            this.getMilitaries();
        },
        methods: {
            getMilitaries() {
                axios.get('http://localhost:8000/api/militaries').then(res => {
                    console.log(res)
                    this.militaries = res.data.militaries;
                });
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>