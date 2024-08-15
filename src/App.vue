<script setup>
import { ref } from 'vue'

import { RouterLink, RouterView } from 'vue-router'

const visible = ref(false)
</script>

<template>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
        <div class="container">
            <a class="navbar-brand" href="#">Vue3 2024 前端新手營 - Eric0823</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse"
                aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation"
                @click="visible = !visible">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse" :class="!visible ? 'collapse' : ''">
                <ul class="navbar-nav mb-2 mb-md-0 mx-auto">
                    <li class="nav-item">
                        <RouterLink to="/" class="nav-link" :class="{ active: $route.path === '/' }"
                            @click="visible = !visible">Home
                        </RouterLink>
                    </li>
                    <li class="nav-item">
                        <RouterLink to="/about" class="nav-link" :class="{ active: $route.path === '/about' }"
                            @click="visible = !visible">
                            About</RouterLink>
                    </li>
                    <li class="nav-item">
                        <RouterLink to="/week01" class="nav-link" :class="{ active: $route.path === '/week01' }"
                            @click="visible = !visible">
                            第一週作業</RouterLink>
                    </li>
                    <li class="nav-item">
                        <RouterLink to="/week02" class="nav-link" :class="{ active: $route.path === '/week02' }"
                            @click="visible = !visible">
                            第二週作業</RouterLink>
                    </li>
                    <li class="nav-item">
                        <RouterLink to="/week03" class="nav-link" :class="{ active: $route.path === '/week03' }"
                            @click="visible = !visible">
                            第三週作業</RouterLink>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    <RouterView />
</template>

<style scoped></style>
