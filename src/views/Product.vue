<template>
  <div class="product">
    <HeaderShayna />

    <!-- Breadcrumb Section Begin -->
    <div class="breadcrumb-section text-left">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text product-more">
              <router-link to="/">
                <i class="fa fa-home"></i>Home
              </router-link>
              <span>Detail</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Product Shop Section Begin -->
    <section class="product-shop spad page-details">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="row">
              <div class="col-lg-6">
                <div class="product-pic-zoom">
                  <img class="product-big-img" :src="image_default" />
                </div>
                <div class="product-thumbs" v-if="productDetail.galleries.length > 0">
                  <carousel
                    :nav="false"
                    :dots="false"
                    :items="3"
                    class="product-thumbs-track ps-slider"
                  >
                    <div
                      v-for="thumbs in productDetail.galleries"
                      :key="thumbs.id"
                      class="pt"
                      @click="changeImage(thumbs.photo)"
                      :class="thumbs.photo == image_default ? 'active' : '' "
                    >
                      <img :src="thumbs.photo" />
                    </div>
                  </carousel>
                </div>
              </div>
              <div class="col-lg-6">
                <div class="product-details text-left">
                  <div class="pd-title">
                    <span>{{ productDetail.type }}</span>
                    <h3>{{ productDetail.name }}</h3>
                  </div>
                  <div class="pd-desc">
                    <p v-html="productDetail.description"></p>
                    <h4>Rp. {{ productDetail.price }}</h4>
                  </div>
                  <div class="quantity">
                    <!-- <router-link to="/cart" class="primary-btn pd-cart">Add To Cart</router-link> -->
                    <a
                      href="#"
                      class="primary-btn pd-cart"
                      @click="saveToCart(productDetail.id, productDetail.name, productDetail.price, productDetail.galleries[0].photo)"
                    >Add To Cart</a>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Product Shop Section End -->

    <RelatedShayna />
    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
import HeaderShayna from "@/components/HeaderShayna.vue";
import FooterShayna from "@/components/FooterShayna.vue";
import RelatedShayna from "@/components/RelatedShayna.vue";

import carousel from "vue-owl-carousel";
import axios from "axios";

export default {
  name: "product",
  components: {
    HeaderShayna,
    FooterShayna,
    RelatedShayna,
    carousel
  },
  data() {
    return {
      image_default: "",
      productDetail: [],
      userCart: []
    };
  },
  methods: {
    changeImage(urlImage) {
      this.image_default = urlImage;
    },
    setDataImage(data) {
      // replace object productDetail dengan data dari API
      this.productDetail = data;
      // replace image_default dengan data dari API
      this.image_default = data.galleries[0].photo;
    },
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
      window.location.reload();
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
      .get("http://127.0.0.1:8000/api/products", {
        params: {
          slug: this.$route.params.slug
        }
      })
      .then(res => this.setDataImage(res.data.data))
      .catch(err => console.log(err));
  }
};
</script>

<style scoped>
.product-thumbs .pt {
  margin-right: 10px;
}
</style>
