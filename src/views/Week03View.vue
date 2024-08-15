<script setup>
import CartMenuComponent from "@/components/CartMenuComponent.vue"
import CartListComponent from "@/components/CartListComponent.vue"
import OrderComponet from "@/components/OrderComponet.vue";
import { computed, ref } from "vue"
const data = [
    {
        "id": 1,
        "name": "珍珠奶茶",
        "description": "香濃奶茶搭配QQ珍珠",
        "price": 50
    },
    {
        "id": 2,
        "name": "冬瓜檸檬",
        "description": "清新冬瓜配上新鮮檸檬",
        "price": 45
    },
    {
        "id": 3,
        "name": "翡翠檸檬",
        "description": "綠茶與檸檬的完美結合",
        "price": 55
    },
    {
        "id": 4,
        "name": "四季春茶",
        "description": "香醇四季春茶，回甘無比",
        "price": 45
    },
    {
        "id": 5,
        "name": "阿薩姆奶茶",
        "description": "阿薩姆紅茶搭配香醇鮮奶",
        "price": 50
    },
    {
        "id": 6,
        "name": "檸檬冰茶",
        "description": "檸檬與冰茶的清新組合",
        "price": 45
    },
    {
        "id": 7,
        "name": "芒果綠茶",
        "description": "芒果與綠茶的獨特風味",
        "price": 55
    },
    {
        "id": 8,
        "name": "抹茶拿鐵",
        "description": "抹茶與鮮奶的絕配",
        "price": 60
    }
]

// 購物車列表
const cart_list = ref([])

// 訂單列表
const order_list = ref([])

// 訂單總價
const order_total = ref(0)

// 訂單備註
const order_note = ref('')

// 動態加入購物車列表 - 並加入預設數量：1
function addCart(item) {
    const existingItem = cart_list.value.find(CartItem => CartItem.id === item.id)
    if (existingItem) {
        // 有重複的話，數量加 1
        existingItem.quantity += 1
    } else {
        // 沒有的話，直接加入 - 數量預設值 1
        cart_list.value.push({ ...item, quantity: 1 })
    }
}

// 計算總金額
const total = computed(() => {
    // 抓取 cart_list 內的所有物件，並計算總金額
    return cart_list.value.reduce((sum, item) => {
        // 總金額 = 總金額 + (單項商品金額 * 數量)
        return sum + (item.price * item.quantity)
        // 0 初始值
    }, 0)
})

// 刪除購物車項目
const removeCart = (item) => {
    const index = cart_list.value.findIndex(cartItem => cartItem.id === item.id);
    if (index !== -1) {
        cart_list.value.splice(index, 1);
    }
}

// 創建訂單
const createOrder = (note) => {
    // COPY 購物車列表 到 訂單列表
    order_list.value = [...cart_list.value]
    // 訂單總金額
    order_total.value = total.value
    // 訂單備註
    order_note.value = note.value
    // 清空購物車
    cart_list.value.splice(0, cart_list.value.length);
}
</script>

<template>
    <div class="text-center">
        <h1 class="mb-5"><strong>第三週作業</strong></h1>
    </div>
    <div id="root">
        <div class="container mt-5">
            <div class="row">
                <div class="col-md-4">
                    <CartMenuComponent :items="data" @addCart="addCart" />
                </div>
                <div class="col-md-8">
                    <CartListComponent :items="cart_list" @createOrder="createOrder" :total="total" @removeCart="removeCart" />
                </div>
            </div>
            <hr />
            <div class="row justify-content-center mb-5">
                <OrderComponet :items="order_list" :order_total="order_total" :order_note="order_note" />
            </div>
        </div>
    </div>
</template>