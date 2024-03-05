<template>
    <div class="me-3">
        <button class="btn btn-primary dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">
            {{ label }}
        </button>
        <ul class="dropdown-menu">
            <li v-for="(item, index) in items" :key="index">
                <a @click="selectItem(item)" class="dropdown-item" href="#">{{ item }}</a>
            </li>
        </ul>
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
    methods: {
        selectItem(item) {
            this.$emit('item-selected', item);
        }
    }
}
</script>