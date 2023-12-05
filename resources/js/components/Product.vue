<template>
    <div>
        <h5 id="associating-form-text-with-form-controls">Name:</h5>
        <h6>{{ product.name }}</h6>
        <h5 id="associating-form-text-with-form-controls">Category:</h5>
        <p>{{ product.category }}</p>
        <h5 id="associating-form-text-with-form-controls">Description:</h5>
        <p v-html="product.description"></p>
        <h5 id="associating-form-text-with-form-controls">Image:</h5>
        <p>
			<img :src="product.image_data ?? 'https://placehold.co/600x400?text=Placeholder'">
		</p>
		<router-link :to="`/`" class="btn btn-danger">Cancel</router-link>
        <router-link :to="`/products/${product.id}/edit`" class="btn btn-primary">Edit</router-link>
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
				product: {}
			}
		},
		async created() {
			try {
			const response = await axios.get(this.api+`products/${this.$route.params.id}`);
			this.product = response.data;
			} catch (error) {
			console.error(error);
			}
		}
	}
</script>