<template>
  <div class="details_section">
    <!-- Start:: cart component -->
    <div class="cart">
      <SidebarComp
        v-model:visible="visibleRight"
        :baseZIndex="10000"
        position="right"
      >
        <!-- Start:: Cart Header -->
        <div class="cart_header">
          <h3>{{ $t("misc.yourCart") }}</h3>
          <button @click="visibleRight = false">
            <i class="pi pi-times"></i>
          </button>
        </div>
        <!-- Start:: Cart Header -->
        <hr />

        <!-- Start:: Cart Products -->
        <div class="cart_products">
          <!-- v-for="product in cart" :key="product.id" -->
          <div class="cart_product" v-for="item in cart" :key="item.id" >
            <div class="product_details">
              <div class="product_image"><img :src="item.image" alt="image" /></div>
              <div class="product_name">
                <h5>{{ item.title }}</h5>
                <h5>$ {{ item.price }} {{ $t("misc.USD") }}</h5>
                <button @click="removeItem()">{{ $t("buttons.remove") }}</button>
              </div>
            </div>
            <div class="product_qty">
              <input type="number" v-model="quantity" class="cart_input"/>
            </div>
          </div>
        </div>
        <!-- End:: Cart Products -->

        <hr />
        <!-- Start:: Cart Footer -->
        <div class="cart_footer">
          <h3>{{ $t("misc.subtotal") }}</h3>
          <h3>Total</h3>
        </div>
        <div class="btn_wrapper">
          <button class="details_btn">
            {{ $t("buttons.continueToCheckout") }}
          </button>
        </div>
        <!-- End:: Cart Footer -->
      </SidebarComp>
    </div>
    <!-- End:: cart component -->

    <!-- Start:: head Section -->
    <div class="head_section">
      <div class="bg-top"></div>
      <div class="bg-botttom"></div>
      <v-container>
        <v-row class="details_product">
          <v-col
            cols="12"
            lg="8"
            md="8"
            xs="12"
            class="images_details"
            style="padding: 0 12px"
          >
            <carousel
              :items-to-show="1"
              :wrap-around="false"
              :transition="1000"
              :autoplay="false"
              class="image_carousal"
            >
              <slide v-for="slide in product.images_details" :key="slide">
                <img :src="slide" alt="" />
              </slide>

              <template #addons>
                <navigation />
                <pagination />
              </template>
            </carousel>
          </v-col>
          <v-col cols="12" lg="4" md="4" xs="12" class="info_wrapper">
            <div class="product_info">
              <h2>{{ product.title }}</h2>
              <h2>${{ product.price }} {{ $t("misc.USD") }}</h2>
              <div class="add_wrapper">
                <label>{{ $t("misc.quantity") }}</label>
                <input type="number" v-model="quantity" required />
                <button @click= "addToCart" class="details_btn">
                  {{ $t("buttons.addToCart") }}
                </button>
              </div>
            </div>
          </v-col>
        </v-row>
        <CartComponent />
      </v-container>
    </div>

    <!-- End:: head Section -->

    <!-- Start:: Description section -->
    <div class="des_wrapper">
      <v-container>
        <h3>{{ $t("misc.des") }}</h3>
        <p>{{ product.des }}</p>
        <h3>{{ $t("misc.shipping") }}</h3>
        <p>{{ product.shipping }}</p>
      </v-container>
    </div>

    <!-- End::Description Section  -->

    <!-- Start:: images -->
    <div class="images_wrapper">
      <v-container>
        <h3>{{ $t("misc.images") }}</h3>
        <v-row>
          <v-col
            cols="12"
            lg="12"
            v-for="image in product.images_details"
            :key="image"
            class="image_container"
          >
            <img :src="image" />
          </v-col>
        </v-row>
      </v-container>
    </div>
    <!-- End:: Images -->
    <!-- Start:: related products -->
    <div class="related_wrapper">
      <v-container>
        <h3>{{ $t("misc.relatedProducts") }}</h3>
        <v-row>
          <v-col
            cols="12"
            lg="4"
            md="6"
            xs="12"
            v-for="product in product.related_products"
            :key="product.id"
          >
            <product-card :product="product" v-if="product.id" />
          </v-col>
        </v-row>
        <!-- router link to all products section -->
        <div class="text_right">
          <router-link to="/" class="back_products">{{
            $t("buttons.backToProducts")
          }}</router-link>
        </div>
      </v-container>
    </div>

    <!-- End:: related products -->
  </div>
</template>

<script>
import ProductCard from "../components/ProductCard.vue";
// import { defineComponent } from "vue";
import { Carousel, Navigation, Slide } from "vue3-carousel";

import "vue3-carousel/dist/carousel.css";

export default {
  components: { ProductCard, Carousel, Slide, Navigation },
  props: ["id"],
  data() {
    return {
      visibleRight: false,
      product: {
        required: true,
        type: Object,
      },
      details: this.$route.params,
    };
  },
  methods: {
    addToCart() {
      this.$store.dispatch("addToCart", this.details);
      this.visibleRight = true;
      // if (this.initialQuantity < this.product.quantity) {
      //   this.initialQuantity++;
      // } else {
      //   this.initialQuantity === this.products.quantity;
      // }
      // this.display = 'block'
    },
    removeItem(){
      this.$store.dispatch("removeItem", this.details)
    }
  },
  computed: {
    products() {
      return this.$store.getters.products;
    },
    cart() {
      return this.$store.getters.cart;
    },
    quantity() {
      return this.$store.getters.quantity;
    },
  },
  mounted() {
    this.product = this.products.find((product) => product.id == this.id);
    this.details = this.product;
  },
};
</script>

<style lang="scss">
.details_section {
  font-family: "normal", sans-serif;
  h3 {
    font-size: 24px;
    line-height: 30px;
    margin: 20px 0;
    color: #5f4d5d;
  }
  p {
    font-size: 23px;
  }
  .details_product {
    .carousel__slide {
      width: 100%;
      height: 500px;
      margin: 20px 0;

      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
      }
    }
    .carousel__prev,
    .carousel__next {
      color: white;
    }
  }
  .head_section {
    position: relative;
    padding: 50px 0;
    .info_wrapper {
      height: 500px;
      margin: 20px 0;
      background-color: #b08ead;
      color: white;

      .product_info {
        display: flex;
        justify-content: center;
        justify-items: center;
        align-items: flex-start;
        flex-direction: column;
        row-gap: 15px;
        margin: 20px;

        h2 {
          font-weight: 100;
          font-size: 38px;
          font-family: sans-serif;
        }
        h2:last-of-type {
          font-size: 26px;
        }
        .add_wrapper {
          width: 100%;
          display: block;
          label {
            margin: 10px 0;
            font-family: Roboto, sans-serif;
            font-size: 12px;
            font-weight: 700;
            letter-spacing: 1.5px;
            text-transform: uppercase;
            display: block;
          }
        }
      }
    }
  }
  .image_container {
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
  .related_wrapper {
    background-color: #f3f3f3;
    font-family: "normal", sans-serif;
    padding-bottom: 50px;
    .text_right {
      text-align: right;
      margin-top: 50px;
      .back_products {
        transition: opacity 200ms ease;
        font-family: Roboto, sans-serif;
        color: #b99bb6;
        font-size: 13.5px;
        font-weight: 700;
        letter-spacing: 1.5px;
        text-transform: uppercase;

        &:hover {
          opacity: 0.8;
        }
      }
    }
  }
}

.bg-top {
  position: absolute;
  left: 0px;
  top: 0px;
  right: 0px;
  height: 56%;
  background-color: #c9adc6;
  z-index: -20;
}
.bg-botttom {
  position: absolute;
  left: 0px;
  right: 0px;
  bottom: 0px;
  height: 44%;
  background-color: #d6bbd3;
  z-index: -20;
}

// Start:: Style Cart

.p-sidebar-right .p-sidebar {
  width: 26rem;
  color: #5f4d5d;
  font-family: "normal", sans-serif !important;
}
.p-sidebar-mask.p-component-overlay {
  z-index: 10001;
  background: #000000d6;
}
.p-sidebar-content {
  background: white;
}
.cart_header,
.cart_footer,
.product_details,
.cart_product {
  display: flex;
  justify-content: space-between;
  align-items: center;
  justify-items: center;
  padding: 20px 10px;
}
.product_details{
  column-gap: 10px;
}
.p-sidebar-header {
  display: none;
}
hr {
  overflow: visible;
  height: 0;
  background: rgb(0 0 0 / 3%);
  height: 1.5px;
  border: none;
}
.product_details {
  font-family: "normal", monospace;
  .product_image {
    width: 60px;
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
  .product_name {
    h5 {
      font-size: 20px;
      color: #5f4d5d;

      &:last-of-type {
        color: #b0b0b0;
        font-size: 16px;
      }
    }
    button {
      color: #5f4d5d;
      transition: opacity 0.3s ease-in-out;

      &:hover {
        opacity: 0.8;
      }
    }
  }
}
.cart_input{
  background: #dddddd38;
}
// End:: Style Cart

// Start:: global btn , input
.btn_wrapper {
  padding: 20px 10px;
  color: white;
}
.details_btn {
  background: #ceb3cb;
  font-family: Roboto, sans-serif;
  display: block;
  width: 100%;
  text-align: center;
  font-size: 15px;
  height: 45px;
  margin: 20px 0;
  transition: 0.3s all ease-in-out;
  &:hover {
    opacity: 0.8;
    transition: 0.3s all ease-in-out;
  }
}
input {
  display: block;
  background: white;
  width: 50px;
  height: 35px;
  outline: none;
  border: none;
  font-size: 20px;
  padding: 5px;
  text-align: center;
}
// End:: global btn
</style>
