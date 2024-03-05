<template>
    <div>
        <div class="card">
            <div class="card-header">
                <h4>Data Militer
                    <NuxtLink to="/militaries/create" class="btn btn-primary float-end">Tambah Data</NuxtLink>
                </h4>
            </div>
            <div class="card-body">
                <div class="card-body-child d-flex justify-content-between align-items-center mb-3">
                    <div class="btn-group">
                        <DropdownFilter :label="'Filter Kondisi'" :items="['Bekas', 'Baru', 'Rusak']" @item-selected="filterByCondition"></DropdownFilter>
    
                        <DropdownFilter :label="'Filter Matra'" :items="['TNI-AU', 'TNI-AD', 'TNI-AL', 'KEMHAN']" @item-selected="filterByMatra"></DropdownFilter>
    
                        <DropdownFilter :label="'Filter Jenis'" :items="['Senjata', 'Kendaraan', 'Pesawat']" @item-selected="filterByJenis"></DropdownFilter>
    
                        <div class="input-group w-25">
                            <input type="text" class="form-control" placeholder="Cari..." v-model="searchQuery">
                            <button class="btn btn-outline-secondary" type="button" @click="searchMilitary">Cari</button>
                        </div>
                    </div>
                    <div class="row align-items-center">
                        <div class="col-md-4">
                            <label for="startDate" class="form-label mb-1">Mulai Tanggal:</label>
                            <input type="date" id="startDate" v-model="startDate" class="form-control">
                        </div>
                        <div class="col-md-4">
                            <label for="endDate" class="form-label mb-1">Sampai Tanggal:</label>
                            <input type="date" id="endDate" v-model="endDate" class="form-control">
                        </div>
                        <div class="col-md-4">
                            <label class="form-label mb-1 w-100"><span class="text-white">.</span></label>
                            <button class="btn btn-primary mt-md-0 mt-2" @click="filterByDateRange">Tampilkan</button>
                        </div>
                    </div>
                </div>
                
                <table class="table table-striped table-bordered">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Nama</th>
                            <th>Jenis</th>
                            <th>Kondisi</th>
                            <th>Tahun Produksi</th>
                            <th>Tanggal Perolehan</th>
                            <th>Matra</th>
                            <th>Gambar</th>
                            <th>Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(military, index) in paginatedMilitaries" :key="index">
                            <td>{{ military.id }}</td>
                            <td>{{ military.nama }}</td>
                            <td>{{ military.jenis }}</td>
                            <td>{{ military.kondisi }}</td>
                            <td>{{ military.tahun_produksi }}</td>
                            <td>{{ military.tanggal_perolehan }}</td>
                            <td>{{ military.matra }}</td>
                            <td>
                                <img :src="getImageUrl(military.gambar)" alt="Military Image" style="max-width: 100px; max-height: 100px;">
                            </td>
                            <td>
                                <NuxtLink :to="`/militaries/${military.id}`" class="btn btn-success btn-sm mx-2">Ubah</NuxtLink>
                                <button type="button" @click="$event => deleteMilitary(military.id)" class="btn btn-danger btn-sm mx-2">Hapus</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
                <nav class="pagination-container" aria-label="Page navigation example">
                    <ul class="pagination justify-content-center">
                        <li class="page-item">
                            <a class="page-link" :class="{ 'disabled': currentPage === 1 }" @click="currentPage -= 1" aria-label="Previous">
                                <span aria-hidden="true">&laquo;</span>
                                <span class="sr-only">Previous</span>
                            </a>
                        </li>
                        <li v-for="page in totalPages" :key="page" class="page-item">
                            <a class="page-link" @click="currentPage = page">{{ page }}</a>
                        </li>
                        <li class="page-item">
                            <a class="page-link" :class="{ 'disabled': currentPage === totalPages }" @click="currentPage += 1" aria-label="Next">
                                <span aria-hidden="true">&raquo;</span>
                                <span class="sr-only">Next</span>
                            </a>
                        </li>
                    </ul>
                </nav>
            </div>
           
        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    import Swal from 'sweetalert2';
    import DropdownFilter from '../../components/DropDownFilter.vue';

    export default {
        name: "militaris",
        components: {
            DropdownFilter
        },
        data() {
            return {
                militaries: [],
                filteredMilitaries: [],
                selectedCondition: null,
                searchQuery: '',
                currentPage: 1,
                itemsPerPage: 10
            }
        },
        computed: {
            totalPages() {
                return Math.ceil(this.filteredMilitaries.length / this.itemsPerPage);
            },
            paginatedMilitaries() {
                const startIndex = (this.currentPage - 1) * this.itemsPerPage;
                const endIndex = startIndex + this.itemsPerPage;
                return this.filteredMilitaries.slice(startIndex, endIndex);
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
            filterByMatra(matra) {
                this.filteredMilitaries = this.militaries.filter(military => military.matra === matra);
            },
            filterByJenis(jenis) {
                
                this.filteredMilitaries = this.militaries.filter(military => military.jenis === jenis);
                console.log(this.militaries)
            },
            filterByDateRange() {
                if (this.startDate && this.endDate) {
                    this.filteredMilitaries = this.militaries.filter(military => {
                        const militaryDate = new Date(military.tanggal_perolehan);
                        const startDate = new Date(this.startDate);
                        const endDate = new Date(this.endDate);

                        return militaryDate >= startDate && militaryDate <= endDate;
                    });
                } else {
                    this.filteredMilitaries = this.militaries;
                }
            },
            deleteMilitary(militaryId) {
                Swal.fire({
                    title: 'Anda yakin?',
                    text: 'Apakah Anda yakin ingin menghapus data ini?',
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Ya, hapus',
                    cancelButtonText: 'Tidak',
                    cancelButtonColor: '#d33',
                    confirmButtonColor: '#3085d6'
                }).then((result) => {
                    if (result.isConfirmed) {
                        axios.delete(`http://localhost:8000/api/militaries/${militaryId}/delete`).then(res => {
                            this.getMilitaries();
                        });
                        Swal.fire({
                            title: 'Selamat!',
                            text: 'Hapus Data Berhasil!',
                            icon: 'success'
                        }).then(() => {
                            window.location.reload(true);
                        });
                    }
                });
            },
            getImageUrl(imageName) {
                return `http://localhost:8000/api/militaries/image/${imageName}`;
            },
            searchMilitary() {
                
                if (this.searchQuery.trim() === '') {
                    this.filteredMilitaries = this.militaries;
                    return;
                }

                const searchTerm = this.searchQuery.toLowerCase().trim();
                
                this.filteredMilitaries = this.militaries.filter(military => {
                    return military.nama.toLowerCase().includes(searchTerm) || 
                           military.jenis.toLowerCase().includes(searchTerm);
                });
            },
        }
    }
</script>

