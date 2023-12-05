<template>
    <div>
        <h2 v-if="isNewProduct">Create Product</h2>
        <h2 v-else>Edit Product</h2>
        <form @submit.prevent="submitForm">
            <div class="mb-3">
                <label for="name" class="form-label">Name:</label>
                <input class="form-control" placeholder="Name" type="text" id="name" v-model="product.name" required />
            </div>
            <div class="mb-3">
                <label for="category_id" class="form-label">Category:</label>
                <select class="form-control" name="category_id" id="category_id" v-model="product.category_id">
                    <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
                </select>
            </div>
            <div class="mb-3">
                <label for="description" class="form-label">Description:</label>
                <textarea placeholder="Description" id="summernote" required></textarea>
                <input type="hidden" id="summernote_text" v-model="product.description">
            </div>
            <div class="mb-3">
                <label for="image_data" class="form-label">Image:</label>
                <input class="form-control" type="file" name="image_data" id="image_data" @change="onFileChange">
            </div>
            <router-link :to="`/`" class="btn btn-danger">Cancel</router-link>
            <button type="submit" v-if="isNewProduct" class="btn btn-primary">Add Product</button>
            <button type="submit" v-else class="btn btn-primary">Update Product</button>
        </form>
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
                categories: [],
                product: {
                    name: '',
                    category_id: '',
                    description: '',
                    image_data: ''
                }
            }
        },
        computed: {
            isNewProduct() {
                return !this.$route.path.includes('edit');
            }
        },
        watch:{
            $route (to, from){
                if (this.$route.path.includes('create')) {
                    this.product.name = '';
                    this.product.category_id = '';
                    this.product.description = '';
                    $('#summernote').summernote("code", '');
                }
            }
        } ,
        async created() {
            if (!this.isNewProduct) {
                const response = await axios.get(this.api+`products/${this.$route.params.id}`);
                this.product = response.data;
            }
            this.getCategories();
            $(function () {
                // Summernote
                $('#summernote').summernote({
                    callbacks: {
                        onKeyup: function(e) {
                            var desc = $(e.target).html();
                            $('#summernote_text').val(desc);
                        }
                    }
                });
                $('#summernote').summernote("code", $('#summernote_text').val());
            })
        },
        methods: {
            async getCategories() {
                try {
                    const response = await axios.get(this.api+'categories');
                    this.categories = response.data;
                } catch (error) {
                    console.error(error);
                }
            },
            async submitForm() {
                this.product.description = $('#summernote_text').val();
                try {
                    if (this.isNewProduct) {
                        await axios.post(this.api+'products', this.product);
                    } else {
                        await axios.put(this.api+`products/${this.$route.params.id}`, this.product);
                    }
                    this.$router.push('/');
                } catch (error) {
                    console.error(error);
                }
            },
            onFileChange(e) {
                var files = e.target.files || e.dataTransfer.files;
                if (!files.length)
                    return;
                this.createImage(files[0]);
            },
            createImage(file) {
                var reader = new FileReader();
                reader.readAsDataURL(file);

                reader.onload = (e) => {
                    this.product.image_data = reader.result;
                };
            },
        }
    }
</script>