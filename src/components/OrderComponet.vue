<template>
    <div class="col-8">
        <div class="alert alert-danger text-center animate__animated animate__flipInX" role="alert" v-show="items.length == 0"> 尚未建立訂單 </div>
    </div>
    <div class="col-8" v-show="items.length != 0">
        <div class="card">
            <div class="card-header align-items-center text-white bg-primary h5">
                <strong>訂單</strong>
            </div>
            <div class="card-body">
                <table class="table table-striped">
                    <thead class="text-center">
                        <tr>
                            <th scope="col" width="50">#</th>
                            <th scope="col">品項</th>
                            <th scope="col">數量</th>
                            <th scope="col">小計</th>
                        </tr>
                    </thead>
                    <tbody class="text-center">
                        <tr v-for="(item, index) in props.items" :key="item.id">
                            <td>{{ index += 1 }}</td>
                            <td>{{ item.name }}</td>
                            <td>{{ item.quantity }}</td>
                            <td>{{ item.price * item.quantity }}</td>
                        </tr>
                    </tbody>
                </table>
                <div class="text-start rounded bg-light p-2 border"><small>備註：<span>{{ props.order_note }}</span></small></div>
                <hr />
                <div class="text-end">
                    <h5>總計：<span>${{ props.order_total }}</span></h5>
                </div>
            </div>
        </div>
    </div>

</template>

<script setup>
// 定義 props 以接收來自父組件的數據
const props = defineProps({
    items: {
        type: Array,
        required: true
    },
    order_note: {
        type: String,
        required: true
    },
    order_total: {
        type: Number,
        required: true
    }
})
</script>