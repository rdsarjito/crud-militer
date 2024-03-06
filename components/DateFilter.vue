<template>
    <div>
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
  </template>
  
<script>
    export default {
        name: 'DateFilter',
        data() {
            return {
            startDate: '',
            endDate: ''
            };
        },
        methods: {
            filterByDateRange() {
                if (this.startDate && this.endDate) {
                    const startDate = new Date(this.startDate);
                    const endDate = new Date(this.endDate);

                    this.$emit('date-filter', { startDate, endDate });
                }
            }
        }
    };
</script>