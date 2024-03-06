<template>
    <div>
        <div class="card">
            <div class="card-header">
                <h4>Data Militer
                    <button class="btn btn-primary float-end" @click="showConfirmationDialog">Cetak Excel</button>
                </h4>
            </div>
            <div class="card-body">
                <button class="btn btn-primary mt-md-0 mt-2" @click="applyMultipleFilters">Tampilkan</button>
                <div class="card-body-child d-flex justify-content-between align-items-center mb-3">
                    <div class="btn-group">
                        <DropdownFilter label="Filter Kondisi" :items="['Bekas', 'Baru', 'Rusak']" @item-selected="filterByCondition" />
    
                        <DropdownFilter label="Filter Matra" :items="['TNI-AU', 'TNI-AD', 'TNI-AL', 'KEMHAN']" @item-selected="filterByMatra" />
    
                        <DropdownFilter label="Filter Jenis" :items="['Senjata', 'Kendaraan', 'Pesawat']" @item-selected="filterByJenis" />
    
                        <div class="row align-items-center">
                            <div class="col-md-6">
                                <label for="startDate" class="form-label mb-1">Dari</label> 
                                <input type="date" id="startDate" v-model="startDate" class="form-control">
                            </div>
                            <div class="col-md-6">
                                <label for="endDate" class="form-label mb-1">Sampai</label>
                                <input type="date" id="endDate" v-model="endDate" class="form-control">
                            </div>
                        </div>
                        
                    </div>
                    <NuxtLink to="/militaries/create" class="btn btn-primary mt-md-0 mt-2">Tambah Data</NuxtLink>
                    
                </div>
                <div class="row align-items-center">
                    <Search @search="searchMilitary"></Search>
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
                        <li class="page-item" :class="{ 'active': currentPage === page }" v-for="page in totalPages" :key="page">
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
    import ExcelJS from 'exceljs';
    import FileSaver from 'file-saver';

    import DropdownFilter from '../../components/DropDownFilter.vue';
    import Search from '../../components/Search.vue';

    export default {
        name: "militaris",
        components: {
            DropdownFilter,
            Search
        },
        data() {
            return {
                militaries: [],
                filteredMilitaries: [],
                selectedCondition: null,
                selectedMatra: null,
                selectedJenis: null,
                searchQuery: '',
                currentPage: 1,
                itemsPerPage: 10,
                selectedConditionOptions: [],
                selectedMatraOptions: [],
                selectedJenisOptions: []
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
            async showConfirmationDialog() {
                const confirmation = await Swal.fire({
                    title: 'Apakah Anda ingin mencetak?',
                    text: 'Anda akan mengunduh data dalam format Excel.',
                    icon: 'question',
                    showCancelButton: true,
                    confirmButtonText: 'Ya, Cetak!',
                    cancelButtonText: 'Batal'
                });

                if (confirmation.isConfirmed) {
                    this.exportToExcel();
                }
            },
            async exportToExcel() {
                // Membuat instance Excel workbook
                const workbook = new ExcelJS.Workbook();
                const worksheet = workbook.addWorksheet('Data_Militer');

                // Untuk menambahkan header ke worksheet
                worksheet.addRow(['ID', 'Nama', 'Jenis', 'Kondisi', 'Tahun Produksi', 'Tanggal Perolehan', 'Matra']);

                // Loop data yang ditampilkan di halaman saat ini
                this.paginatedMilitaries.forEach(military => {
                    worksheet.addRow([
                        military.id,
                        military.nama,
                        military.jenis,
                        military.kondisi,
                        military.tahun_produksi,
                        military.tanggal_perolehan,
                        military.matra
                    ]);
                });

                // Untuk menyimpan workbook ke file Excel
                const buffer = await workbook.xlsx.writeBuffer();
                const blob = new Blob([buffer], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' });
                FileSaver.saveAs(blob, 'Data_Militer.xlsx');
            },
            getMilitaries() {
                axios.get(`http://localhost:8000/api/militaries`).then(res => {
                    this.militaries = res.data.militaries;
                    this.filteredMilitaries = this.militaries;

                    this.selectedConditionOptions = [...new Set(this.militaries.map(military => military.kondisi))];
                    this.selectedMatraOptions = [...new Set(this.militaries.map(military => military.matra))];
                    this.selectedJenisOptions = [...new Set(this.militaries.map(military => military.jenis))];
                });
            },
            filterByCondition(condition) {
                this.selectedCondition = condition;
                // this.applyMultipleFilters();
            },

            filterByMatra(matra) {
                this.selectedMatra = matra;
                // this.applyMultipleFilters();
            },
            filterByJenis(jenis) {
                this.selectedJenis = jenis;
                // this.applyMultipleFilters();
            },

            applyMultipleFilters() {
                console.log
                this.filteredMilitaries = this.militaries;

                if (this.selectedCondition) {
                    this.filteredMilitaries = this.filteredMilitaries.filter(military => military.kondisi === this.selectedCondition);
                }

                if (this.selectedMatra) {
                    this.filteredMilitaries = this.filteredMilitaries.filter(military => military.matra === this.selectedMatra);
                }

                if (this.selectedJenis) {
                    this.filteredMilitaries = this.filteredMilitaries.filter(military => military.jenis === this.selectedJenis);
                }

                if (this.startDate && this.endDate) {
                    this.filteredMilitaries = this.filteredMilitaries.filter(military => {
                        const militaryDate = new Date(military.tanggal_perolehan);
                        const startDate = new Date(this.startDate);
                        const endDate = new Date(this.endDate);

                        return militaryDate >= startDate && militaryDate <= endDate;
                    });
                }
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
            searchMilitary(query) {
                if (query.trim() === '') {
                    this.filteredMilitaries = this.militaries;
                    return;
                }

                const searchTerm = query.toLowerCase().trim();
                
                this.filteredMilitaries = this.militaries.filter(military => {
                    return military.nama.toLowerCase().includes(searchTerm) || 
                        military.jenis.toLowerCase().includes(searchTerm);
                });
            }
        }
    }
</script>