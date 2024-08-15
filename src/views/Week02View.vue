<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
// api 網址
const api_site = 'https://todolist-api.hexschool.io'

// 顯示模態視窗
const message = ref('')
const MessageModal = (msg) => {
    message.value = msg
    const modalElement = document.getElementById('MessageModal')
    if (window.bootstrap && window.bootstrap.Modal) {
        const bootstrapModal = new window.bootstrap.Modal(modalElement)
        bootstrapModal.show()
    } else {
        console.error('Bootstrap is not loaded')
    }
}

// 註冊
const emailSingup = ref('')
const passwordSignUp = ref('')
const nicknameSignUp = ref('')
const signUpSwitch = ref(false)
const messageSignUp = ref('')

// 註冊頁面 - 觸發按鈕
const signupFn = () => {
    signUpSwitch.value = true
    signInSwitch.value = false
}
// 註冊頁面 - 離開
const signUpExit = () => {
    signUpSwitch.value = false
    signInSwitch.value = true
}

// 註冊頁面 - 送出
const signUpSubmit = async () => {
    try {
        const response = await axios.post(`${api_site}/users/sign_up`, {
            email: emailSingup.value,
            password: passwordSignUp.value,
            nickname: nicknameSignUp.value
        })
        messageSignUp.value = '註冊成功 UID：' + response.data.uid
        signUpSwitch.value = false
        signInSwitch.value = true
        emailSingup.value = ''
        passwordSignUp.value = ''
    } catch (error) {
        messageSignUp.value = '註冊失敗：' + error.message
        signUpSwitch.value = true
        signInSwitch.value = false
    }
    MessageModal(messageSignUp.value)
}

// 登入
const signInSwitch = ref(true)
const emailSignin = ref('')
const passwordSignin = ref('')
const nickname = ref('')
const token = ref('')
const signInSubmit = async () => {
    try {
        // 登入 - 發送登入請求至 API
        const response = await axios.post(`${api_site}/users/sign_in`, {
            email: emailSignin.value,
            password: passwordSignin.value
        })

        // 登入成功 - 儲存 token
        token.value = response.data.token
        // 登入成功 - 顯示帳號
        nickname.value = response.data.nickname
        // 登入成功 - 顯示登入成功
        MessageModal('<h1>登入成功!</h1>')

        // 登入成功 - 關閉登入視窗、清空輸入
        signInSwitch.value = false
        emailSignin.value = ''
        passwordSignin.value = ''

        // 登入成功 - 顯示 TODO LIST
        todoListSwitch.value = true
        getTodos()
    } catch (error) {
        // 登入失敗 - 不儲存 token
        token.value = ''

        // 登入失敗 - 顯示登入失敗
        MessageModal('登入失敗：<br>' + error.message)

        // 登入失敗 - 顯示登入視窗
        signInSwitch.value = true
    }
}

// 登出
const signOut = async () => {
    try {
        await axios.post(
            `${api_site}/users/sign_out`,
            {},
            {
                headers: {
                    Authorization: token.value
                }
            }
        )
        signInSwitch.value = true
        todoListSwitch.value = false
        token.value = ''
    } catch (error) {
        token.value = '登出錯誤: ' + error.message
    }
}
// 驗證 Token
//const checkMsg = ref('')
const checkToken = async () => {
    try {
        const response = await axios.get(`${api_site}/users/checkout`, {
            headers: {
                Authorization: token.value
            }
        })

        MessageModal(`<p><h4><strong>Token</strong></h4>${token.value}</p><h4><strong>驗證結果</strong></h4>${response.data.status}`)
    } catch (error) {
        MessageModal(`<p><h4><strong>Token</strong></h4>${token.value}</p><h4><strong>驗證結果</strong></h4>${error.message}`)
    }
}

// TODO LIST
const todoListSwitch = ref(false)
const todos = ref([])
const newTodo = ref('')
const editTodoId = ref('')
const editItemTemp = ref('')
const hiddenId = ref('')

// 取得 TODO LIST
const getTodos = async () => {
    const response = await axios.get(`${api_site}/todos`, {
        headers: {
            Authorization: token.value
        }
    })
    todos.value = response.data.data
}

// 新增 TODO
const addTodo = async () => {
    console.log(newTodo.value)
    if (!newTodo.value) return
    const todo = {
        content: newTodo.value
    }
    await axios.post(`${api_site}/todos`, todo, {
        headers: {
            Authorization: token.value
        }
    })
    newTodo.value = ''
    getTodos()
}

// 刪除 TODO
const deleteTodo = async (id) => {
    await axios.delete(`${api_site}/todos/${id}`, {
        headers: {
            Authorization: token.value
        }
    })
    getTodos()
}

// 開啟編輯 TODO Item 區域
const editTodoArea = async (id) => {
    editTodoId.value = id
    hiddenId.value = id
    editItemTemp.value = todos.value.find((item) => item.id === id).content
}

// 送出更新 TODO Item
const editTodoItemUpdate = async (id) => {
    try {
        // 偵測是否有輸入
        if (editItemTemp.value === '') return MessageModal('<h3>請輸入內容!</h3>')
        await axios.put(
            `${api_site}/todos/${id}`,
            {
                content: editItemTemp.value
            },
            {
                headers: {
                    Authorization: token.value
                }
            }
        )
        editTodoId.value = ''
        hiddenId.value = ''
    } catch (error) {
        console.log(error)
    }

    getTodos()
}

// 取消編輯 TODO Item
const cancelEditTodo = () => {
    // 偵測是否有輸入
    if (editItemTemp.value === '') return MessageModal('<h3>請輸入內容!</h3>')
    editTodoId.value = ''
    hiddenId.value = ''
    editItemTemp.value = ''
}

const toggleStatus = async (id) => {
    await axios.patch(
        `${api_site}/todos/${id}/toggle`,
        {},
        {
            headers: {
                Authorization: token.value
            }
        }
    )
    getTodos()
}

const TodoToken = document.cookie
    .split('; ')
    .find((row) => row.startsWith('hexschoolTodo='))
    ?.split('=')[1]

onMounted(() => {
    if (TodoToken) {
        token.value = TodoToken
    }
})
</script>

<template>
    <div class="text-center">
        <h1 class="mb-5"><strong>第二週作業</strong></h1>
    </div>
    <div class="row justify-content-center" v-if="signInSwitch">
        <div class="col-8 col-lg-5">
            <div class="card">
                <div class="card-header bg-light h3 text-center">
                    <strong>登入 - Todo List</strong>
                </div>
                <div class="card-body">
                    <form @submit.prevent="signInSubmit">
                        <div class="mb-3">
                            <label for="emailSignin" class="form-label"><strong>E-Mail 帳號</strong></label>
                            <input type="text" class="form-control" id="emailSignin" placeholder="請輸入 E-Mail 帳號..." v-model="emailSignin" required />
                        </div>
                        <div class="mb-3">
                            <label for="passwordSignin" class="form-label"><strong>密碼</strong></label>
                            <input type="password" class="form-control" id="passwordSignin" placeholder="請輸入密碼..." v-model="passwordSignin" required />
                        </div>
                        <div class="mb-3 text-end">
                            <a class="btn btn-danger" href="#" role="button" @click="signupFn">註冊帳號</a>
                        </div>
                        <div class="d-grid">
                            <button type="submit" class="btn btn-lg btn-primary">登入</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>

    <div class="row justify-content-center" v-if="signUpSwitch">
        <div class="col-8 col-lg-5">
            <div class="card">
                <div class="card-header bg-light h3 text-center">
                    <strong>註冊帳號 - Todo List</strong>
                </div>
                <div class="card-body">
                    <form @submit.prevent="signUpSubmit">
                        <div class="mb-3">
                            <label for="emailSingup" class="form-label"><strong>E-mail 帳號</strong></label>
                            <input type="text" class="form-control" id="emailSingup" placeholder="E-Mail..." v-model="emailSingup" required />
                        </div>
                        <div class="mb-3">
                            <label for="passwordSignUp" class="form-label"><strong>密碼</strong></label>
                            <input type="password" class="form-control" id="passwordSignUp" placeholder="密碼..." v-model="passwordSignUp" required />
                        </div>
                        <div class="mb-3">
                            <label for="nicknameSignUp" class="form-label"><strong>暱稱</strong></label>
                            <input type="text" class="form-control" id="nicknameSignUp" placeholder="暱稱..." v-model="nicknameSignUp" required />
                        </div>
                        <div class="d-grid">
                            <button class="btn btn-danger mb-2" role="button" type="submit">註冊帳號</button>
                            <a class="btn btn-secondary" href="#" role="button" @click="signUpExit">取消離開</a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
    <div class="row justify-content-center" v-if="todoListSwitch">
        <div class="col-8 col-lg-5">
            <div class="card">
                <div class="card-header bg-light h3 text-center">
                    <strong>Todo List</strong>
                </div>
                <div class="card-body">
                    <div class="row">
                        <div class="col-12">
                            <div class="float-start"><i class="bi bi-person-badge"></i> {{ nickname }}</div>
                            <div class="float-end">
                                <button type="button" class="btn btn-sm btn-outline-danger me-1" @click="signOut"><i class="bi bi-door-open"></i> 登出</button>
                                <button type="button" class="btn btn-sm btn-outline-danger" @click="checkToken"><i class="bi bi-shield-lock"></i> 驗證</button>
                            </div>
                        </div>
                        <div class="col-12 text-center pt-5 pb-2 h2"><i class="bi bi-pencil-square"></i> 我的待辦事項</div>
                    </div>
                    <form>
                        <div class="input-group mb-3">
                            <input type="text" class="form-control" placeholder="新增待辦事項..." aria-label="新增待辦事項..." aria-describedby="addTodoList" v-model="newTodo" />
                            <button class="btn btn-outline-success" type="button" id="addTodoList" @click="addTodo"><i class="bi bi-plus-circle"></i> 新增</button>
                        </div>
                        <hr />
                        <table class="table text-center table-striped align-middle">
                            <thead>
                                <tr>
                                    <th scope="col" style="width: 50px">#</th>
                                    <th scope="col">項目</th>
                                    <th scope="col" style="width: 90px">功能</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(item, index) in todos" :key="index">
                                    <th scope="col" style="width: 50px">1</th>
                                    <td class="text-start">
                                        <div class="input-group input-group-sm" v-if="editTodoId === item.id">
                                            <input type="text" class="form-control" placeholder="..." v-model="editItemTemp" />
                                            <button class="btn btn-sm btn-outline-secondary" type="button" @click="editTodoItemUpdate(item.id)"><i class="bi bi-check-lg"></i></button>
                                            <button class="btn btn-sm btn-outline-secondary" type="button" @click="cancelEditTodo"><i class="bi bi-x-lg"></i></button>
                                        </div>
                                        <div class="form-check" v-else>
                                            <input @change="toggleStatus(item.id)" v-model="item.status" class="form-check-input" type="checkbox" />
                                            <label class="form-check-label{{ item.id }}"> {{ item.content }} </label>
                                        </div>
                                    </td>
                                    <td>
                                        <button type="button" class="btn btn-success btn-sm me-1" @click="editTodoArea(item.id)" v-show="item.id != hiddenId"><i class="bi bi-pencil"></i></button>
                                        <button type="button" class="btn btn-danger btn-sm" @click="deleteTodo(item.id)"><i class="bi bi-trash"></i></button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </form>
                </div>
            </div>
        </div>
    </div>

    <!-- Model message -->
    <div class="modal fade" id="MessageModal" tabindex="-1" aria-labelledby="MessageModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content modal-dialog-scrollable">
                <div class="modal-header">
                    <h5 class="modal-title" id="MessageModalLabel"><strong>Todo List - 通知</strong></h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body text-center" v-html="message"></div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">關閉</button>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.modal-body {
    word-wrap: break-word;
    word-break: break-all;
    white-space: normal;
}
</style>
