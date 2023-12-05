<template>
    <div class="card">
        <div class="card-header">
            <router-link to="/products/create" class="btn btn-primary">Create</router-link>
        </div>
        <div class="card-body">
            <div class="container-fluid">
                <router-view></router-view>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'App'
    }
</script>