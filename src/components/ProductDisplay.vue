<script>
export default {
  name: "ProductDisplay",
  data() {
    return {
      products: {
        data: {
          category: "",
        },
      },
      productId: 0,
      Loading: false,
      setRating: 0,
      setRatingEmpty: 0,
      productUnavailable: 0,
    };
  },

  methods: {
//mengambil data dari API
    async getProductApi() {
      const api = await fetch(
        `https://fakestoreapi.com/products/${this.productId}`
      );
      const result = await api.json();
      return result;
    },

//mendapatkan ID Product
    async getProductId() {
      this.Loading = true;

      if (this.productId !== 20) {
        this.productId++;
      } else {
        this.productId = 1;
      }

      let data = await this.getProductApi();

      if (
        data.category === "men's clothing" || data.category === "women's clothing")
      {
        this.products = { data };
        this.productUnavailable = true;
        this.setRating = Math.round(data.rating.rate);
      } else {
        this.products = {};
        this.productUnavailable = false;
      }
      this.Loading = false;
    },
  },

//Menampilkan produk kembali saat di refresh
  mounted() {
    this.getProductId();
  },
};
</script>

<template>
  <div class="container bg-netral">

<!--menampilkan loading-->
    <div v-if="Loading" class="card">

<!--menampilkan produk unavailable-->
      <div v-if="!productUnavailable" class="product-unavailable-container">
        <div class="product-text">
          <div class="cta">
            <div class="loader"></div>
          </div>
        </div>
      </div>

      <div v-else class="loader">
        <div class="loader"></div>
      </div>
    </div>


    <div
      v-else
      :class="{
        container: true,
        'bg-unavailable': !productUnavailable,
        'bg-men-color':
          products.data && products.data.category === 'men\'s clothing',
        'bg-women-color':
          products.data && products.data.category === 'women\'s clothing',
      }"
    >
      <div class="overlay">
        <img src="../assets/background.svg" alt="bg-image" />
      </div>
      <div class="card">
        
        <div v-if="!productUnavailable" class="product-unavailable-container">
          <div class="overlay">
            <img src="../assets/unavailable.png" alt="bg-unvailable-product" />
          </div>
          <div class="product-text">
            <p>This product is Unvailable to show</p>
            <div class="cta">
              <button class="cta-next" type="button" @click="getProductId()">
                Next Product
              </button>
            </div>
          </div>
        </div>


<!--menampilkan produk berdasarkan kategori-->
        <div v-else class="product-container">
          <div class="product-image">
            <img :src="products.data.image" alt="image-product" />
          </div>
          <div class="product-text">
            <div class="details">
              <h4
                :class="{
                  title: true,
                  'font-men': products.data.category === 'men\'s clothing',
                  'font-women': products.data.category === 'women\'s clothing',
                }"
              >
                {{ products.data.title }}
              </h4>
              <div class="sub-title">
                <span>{{ products.data.category }}</span>
                <div class="rating">
                  <span>{{ products.data.rating.rate }}/5 </span>
                  <div class="rating">
                    <div
                      v-for="setRate in setRating"
                      :key="setRate"
                      :class="{
                        circle: true,
                        'bg-navy': products.data.category === 'men\'s clothing',
                        'bg-magenta':
                          products.data.category === 'women\'s clothing',
                      }"
                    ></div>

                    <div
                      v-for="setRateEmpty in setRatingEmpty"
                      :key="setRateEmpty"
                      :class="{
                        circleborder: true,
                        'border-men':
                          products.data.category === 'men\'s clothing',
                        'border-women':
                          products.data.category === 'women\'s clothing',
                      }"
                    ></div>
                  </div>
                </div>
              </div>
              <div class="description">
                <p>{{ products.data.description }}</p>
              </div>
            </div>
            <div class="action">
              <span
                :class="{
                  price: true,
                  'font-men': products.data.category === 'men\'s clothing',
                  'font-women': products.data.category === 'women\'s clothing',
                }"
              >
                $ {{ products.data.price }}</span
              >
              <div class="cta">
                <button
                  :class="{
                    'cta-buy': true,
                    'bg-navy btn-buy-men':
                      products.data.category === 'men\'s clothing',
                    'bg-magenta':
                      products.data.category === 'women\'s clothing',
                  }"
                  type="button"
                >
                  Buy Now
                </button>
                <button
                  :class="{
                    'cta-next': true,
                    'font-men border-men btn-men':
                      products.data.category === 'men\'s clothing',
                    'font-women border-women btn-women':
                      products.data.category === 'women\'s clothing',
                  }"
                  type="button"
                  @click="getProductId()"
                >
                  Next Product
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@import "../assets/style/page.css";
</style>