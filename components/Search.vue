<template>
    <div class="search-container">
        <div class="input-group">
            <input type="text" class="form-control" placeholder="Cari..." v-model="searchQuery">
            <button class="btn btn-outline-secondary" type="button" @click="search">Cari</button>
        </div>
    </div>
</template>
  
<script>
    export default {
        name: 'Search',
        data() {
            return {
                searchQuery: ''
            };
        },
        methods: {
            search() {
                this.$emit('search', this.searchQuery.trim());
            }
        }
    };
</script>
  