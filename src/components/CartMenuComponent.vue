<template>
    <div class="list-group">
        <a href="#" v-for="item in props.items" :key="item.id" class="list-group-item list-group-item-action" @click.prevent="addCartItem(item)">
            <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">{{ item.name }}</h5>
                <small>{{ item.price }}</small>
            </div>
            <p class="mb-1">{{ item.description }}</p>
        </a>
    </div>
</template>

<script setup>
// 定義 props 以接收來自父組件的數據
const props = defineProps({
    items: {
        type: Array,
        required: true
    }
})

// 定義可以發送的事件名稱
const emit = defineEmits(['addCart'])

// 發送 'addCart' 事件
const addCartItem = (item) => {
    //console.log(item)
    emit('addCart', item)  // 發送 'addCart' 事件，並攜帶 item 作為參數
}

</script>