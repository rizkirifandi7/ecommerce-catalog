<script>
export default {
    name: 'ProductDisplay',
    data() {
        return {
            isLoading: false,
            index: 0,
            isProductAvailable: false,
            product: {}
        }
    },
    computed: {
            productClass() {
                const category = this.product.data ? this.product.data.category : '';
                return {
                    'bg-blue': category === "men's clothing",
                    'bg-pink': category === "women's clothing",
                };
            },
            fontColor() {
                return this.product.data.category === 'men\'s clothing' ? 'font-navy' : 'font-magenta';
            },
            buttonColor() {
                return this.product.data.category === 'men\'s clothing' ? 'bg-navy' : 'bg-magenta';
            },
            nextProductButtonClass() {
                return this.product.data.category === 'men\'s clothing' ? 'border-navy font-navy' : 'border-magenta font-magenta';
            }
        },
    methods: {
        async fetchAPI() {
            try {
                const response = await fetch(`https://fakestoreapi.com/products/${this.index}`);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                const result = await response.json();
                return result;
            } catch (error) {
                console.error('Failed to fetch product:', error);
                return null;
            }
        },
        async getOneProduct() {
            this.isLoading = true;

            this.index = this.index !== 20 ? this.index + 1 : 1;

            const data = await this.fetchAPI();
            this.isProductAvailable = data && (data.category === "men's clothing" || data.category === "women's clothing");

            if (this.isProductAvailable) {
                this.product = { data };
            }

            this.isLoading = false;
        },

    },
    
    mounted() {
        this.getOneProduct();
    },
}
</script>

<template>
    <div class="container">
        <div v-if="isLoading" class="card">
            <div class="product-container">
                <div class="skeleton-image"></div>
                <div class="skeleton-details">
                    <div class="skeleton-details-product"></div>
                    <div class="skeleton-details-button"></div>
                </div>
            </div>
        </div>
        <div v-else class="container" :class="[productClass, !isProductAvailable ? 'bg-gray' : '']">
            <div class="bg-content">
                <img src="../assets/bg-shape.svg" alt="background bg-content">
            </div>
            <div class="card">
                <div v-if="!isProductAvailable" class="product-unavailable-container">
                    <div class="bg-content">
                        <img src="../assets/bg-sad.svg" alt="background unavailable product" srcset="">
                    </div>
                    <div class="product-unavailable">
                        <p>This product is unavailable to show</p>
                        <div class="btn">
                            <button type="button" @click="getOneProduct()" class="btn-next">Next Product</button>
                        </div>
                    </div>
                </div>
                <div v-else class="product-container">
                    <div class="product-image">
                        <img :src="product.data.image" alt="">
                    </div>
                    <div class="product-details">
                        <div class="product-content">
                            <h3 :class="fontColor" class="title">{{ product.data.title }}</h3>
                            <div class="category-title">
                                <span>{{ product.data.category }}</span>
                                <div class="rating">
                                    <span>{{ product.data.rating.rate }}/5</span>
                                    <div class="rating">
                                    <span 
                                        v-for="index in Math.round(product.data.rating.rate)" 
                                        :key="'filled' + index" 
                                        :class="product.data.category === 'men\'s clothing' ? 'bg-navy' : 'bg-magenta'" 
                                        class="circle"
                                    ></span>
                                    <span 
                                        v-for="index in (5 - Math.round(product.data.rating.rate))" 
                                        :key="'empty' + index" 
                                        class="circle empty"
                                    ></span>
                                </div>
                                </div>
                            </div>
                            <div class="description">
                                <p>{{ product.data.description}}</p>
                            </div>
                        </div>
                        <div class="button-content">
                            <span :class="fontColor" class="price">${{ product.data.price }}</span>
                            <div class="btn">
                                <button type="button" :class="buttonColor" class="btn-buy">Buy Now</button>
                                <button type="button" @click="getOneProduct()" :class="nextProductButtonClass" class="btn-next">Next Product</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
    @import '../assets/style/page.css';
</style>