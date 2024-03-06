<template>
    <div class="me-3">
        <label class="form-label mb-1">{{ label }}</label>
        <select class="form-control" v-model="selectedItem" @change="selectItem">
            <option v-for="(item, index) in items" :key="index" :value="item">{{ item }}</option>
        </select>
    </div>
</template>

<script>
export default {
    props: {
        label: {
            type: String,
            required: true
        },
        items: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            selectedItem: null
        }
    },
    methods: {
        selectItem() {
            this.$emit('item-selected', this.selectedItem);
        }
    }
}
</script>