<template>
  <!--Banner Section Begin -->
  <section class="women-banner spad">
    <div class="container-fluid">
      <div class="row">
        <div class="col-lg-12 mt-5" v-if="products.length > 0">
          <carousel :nav="false" :autoplay="true" :items="4" :dots="true">
            <div class="product-item" v-for="itemProduct in products" :key="itemProduct.id">
              <div class="pi-pic">
                <img v-bind:src="itemProduct.galleries[0].photo" />
                <ul>
                  <li class="w-icon active">
                    <a
                      href="javascript:void(0)"
                      @click="saveToCart(itemProduct.id, itemProduct.name, itemProduct.price, itemProduct.galleries[0].photo)"
                    >
                      <i class="icon_bag_alt"></i>
                    </a>
                  </li>
                  <li class="quick-view">
                    <router-link :to="'/product/'+itemProduct.slug">+ Quick View</router-link>
                  </li>
                </ul>
              </div>
              <div class="pi-text">
                <div class="catagory-name">{{ itemProduct.type }}</div>
                <router-link :to="'/product/'+itemProduct.slug">
                  <h5>{{ itemProduct.name }}</h5>
                </router-link>
                <div class="product-price">
                  Rp. {{ itemProduct.price }}
                  <!-- <span>$35.00</span> -->
                </div>
              </div>
            </div>
          </carousel>
        </div>
        <div class="col-lg-12 mt-5" v-else>
          <p>Produk terbaru belum tersedia untuk saat ini</p>
        </div>
      </div>
    </div>
  </section>
  <!-- Banner Section End -->
</template>

<script>
import carousel from "vue-owl-carousel";
import axios from "axios";

export default {
  name: "WomanShayna",
  components: {
    carousel
  },
  data() {
    return {
      products: [],
      userCart: []
    };
  },
  methods: {
    saveToCart(productId, productName, productPrice, productPhoto) {
      var storedProduct = {
        id: productId,
        name: productName,
        price: productPrice,
        photo: productPhoto
      };
      this.userCart.push(storedProduct);
      const parsed = JSON.stringify(this.userCart);
      localStorage.setItem("userCart", parsed);
      // window.location.reload();
    }
  },
  mounted() {
    if (localStorage.getItem("userCart")) {
      try {
        this.userCart = JSON.parse(localStorage.getItem("userCart"));
      } catch (e) {
        localStorage.removeItem("userCart");
      }
    }

    axios
      .get("http://127.0.0.1:8000/api/products")
      .then(res => (this.products = res.data.data.data))
      .catch(err => console.log(err));
  }
};
</script>

<style scoped>
.product-item {
  margin: 10px;
}
</style>