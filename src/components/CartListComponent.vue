<template>
    <div class="rounded bg-light p-4 border">
        <table class="table table-striped">
            <thead class="table-dark">
                <tr class="text-center">
                    <th scope="col" width="50">操作</th>
                    <th scope="col" width="140">品項</th>
                    <th scope="col">描述</th>
                    <th scope="col" width="90">數量</th>
                    <th scope="col" width="90">單價</th>
                    <th scope="col" width="90">小計</th>
                </tr>
            </thead>
            <tbody class="align-middle">
                <tr v-for="item in props.items" :key="item.id" class="animate__animated animate__fadeIn">
                    <td class="text-center"><button type="button" class="btn btn-sm" @click="removeCartItem(item)">x</button></td>
                    <td class="text-center">{{ item.name }}</td>
                    <td><small>{{ item.description }}</small></td>
                    <td class="text-center">
                        <select class="form-select" v-model="item.quantity">
                            <option value="1">1</option>
                            <option value="2">2</option>
                            <option value="3">3</option>
                            <option value="4">4</option>
                            <option value="5">5</option>
                            <option value="6">6</option>
                            <option value="7">7</option>
                            <option value="8">8</option>
                            <option value="9">9</option>
                            <option value="10">10</option>
                        </select>
                    </td>
                    <td class="text-center">{{ item.price }}</td>
                    <td class="text-center">{{ item.price * item.quantity }}</td>
                </tr>
            </tbody>
        </table>
        <div class="mt-3 alert alert-danger text-center animate__animated animate__flipInX" role="alert" v-show="items.length == 0"> 請選擇商品 </div>
        <div class="text-end mb-3" v-show="items.length != 0">
            <h5>總計: <span>${{ props.total }}</span></h5>
        </div>
        <textarea class="form-control mb-3" rows="3" placeholder="備註" v-model="cart_note" v-show="items.length != 0"></textarea>
        <div class="text-end" v-show="items.length != 0">
            <button class="btn btn-primary" @click.prevent="createOrderBtn"><i class="bi bi-cart-check"></i> 送出</button>
        </div>
    </div>

</template>

<script setup>
import { ref } from "vue"
// 定義 props 以接收來自父組件的數據
const props = defineProps({
    total: {
        type: Number,
        required: true
    },
    items: {
        type: Array,
        required: true
    }
})

const cart_note = ref('')

// 定義可以發送的事件名稱
const emit = defineEmits(['removeCart', 'createOrder'])
// 發送 'removeCart' 事件
const removeCartItem = (item) => {
    //console.log(item)
    emit('removeCart', item)  // 發送 'removeCart' 事件，並攜帶 item 作為參數
}
// 發送 'createOrder' 事件
const createOrderBtn = () => {
    emit('createOrder', cart_note) // 發送 'createOrder' 事件，並攜帶 cart_note 作為參數
    cart_note.value = ''
}
</script>

<style></style>