<template>
    <div>
        <div class="row">
            <div class="col-5">
                <input placeholder="Search by Name or Description" type="text" class="form-control" v-model="search" @keyup.enter="createProduct(false)">
            </div>
            <div class="col-5">
                <select class="form-control" name="category_id" id="category_id" v-model="category_id">
                    <option value="">- SELECT CATEGORY -</option>
                    <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
                </select>
            </div>
            <div class="col-2">
                <button class="btn btn-danger w-50" @click="createProduct(true)">Clear</button>
                <button class="btn btn-primary w-50" @click="createProduct(false)">Search</button>
            </div>
        </div>
        <table class="table table-hover table-striped display m-0">
            <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Name</th>
                    <th scope="col">Category</th>
                    <th scope="col">Description</th>
                    <th scope="col">Actions</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="product in products" :key="product.id">
                    <td>{{ product.id }}</td>
                    <td>{{ product.name }}</td>
                    <td>{{ product.category.name }}</td>
                    <td>
                        <span v-html="product.description"></span>
                    </td>
                    <td>
                        <div class="row gap-3">
                            <router-link :to="`/products/${product.id}`" class="p-2 col border btn btn-primary">View</router-link>
                            <router-link :to="`/products/${product.id}/edit`" class="p-2 col border btn btn-success">Edit</router-link>
                            <button @click="deleteProduct(product.id)" type="button" class="p-2 col border btn btn-danger">Delete</button>
                        </div>
                    </td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th colspan="5">
                        <span v-for="(page, index) in pagination.links" :key="index">
                            <button :disabled="page.label == '...'" @click="createProduct(false, page.label, pagination.current_page, pagination.last_page)" :class="pagination.current_page == index ? 'btn btn-primary' : 'btn btn-light'" v-html="page.label"></button>
                        </span>
                    </th>
                </tr>
            </tfoot>
        </table>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        props: {
            api: String
        },
        data() {
            return {
                search: '',
                category_id: 0,
                categories: [],
                products: [],
                pagination: [],
            }
        },
        created() {
            this.createProduct(true);
            this.getCategories();
        },
        methods: {
            async createProduct(isClear, page, current_page, last_page) {
                try {
                    if (page == "Next &raquo;") {
                        page = current_page + 1;
                        if (page != last_page) {
                            page = last_page;
                        }
                    } else if (page == "&laquo; Previous") {
                        page = current_page - 1;
                    }
                    console.log(isClear);

                    if (isClear) {
                        this.search = '';
                        this.category_id = '';
                    }
                    const response = await axios.get(this.api+'products', {
                        params: { page, search: this.search, category_id: this.category_id }
                    });
                    this.pagination = response.data;
                    this.products = response.data.data;
                } catch (error) {
                    console.error(error);
                }
            },
            async deleteProduct(id) {
                try {
                    await axios.delete(this.api+`products/${id}`);
                    this.products = this.products.filter(product => product.id !== id);
                } catch (error) {
                    console.error(error);
                }
            },
            async getCategories() {
                try {
                    const response = await axios.get(this.api+'categories');
                    this.categories = response.data;
                } catch (error) {
                    console.error(error);
                }
            },
        }
    }
</script>