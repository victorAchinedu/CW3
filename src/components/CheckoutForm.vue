<template>
  <div class="checkout-form">
    <div class="checkout">
      <h2>Checkout</h2>
      <p>
        <strong>First Name:</strong>
        <input v-model.trim="order.firstName" />
      </p>
      <p>
        <strong>Last Name:</strong>
        <input v-model.trim="order.lastName" />
      </p>
      <p>
        <strong>Phone:</strong>
        <input v-model.number="order.phone" type="tel" />
      </p>
      <p v-if="!isFormValid" style="color: red;">Please fill in all fields.</p>
      <h2>Order Information</h2>
      <p>First Name: {{ order.firstName }}</p>
      <p>Last Name: {{ order.lastName }}</p>
      <p>Phone Number: {{ order.phone }}</p>
      <button @click="submitOrderForm" :disabled="!isFormValid">Place Order</button>
      <p v-if="orderSubmitted">Order Submitted!</p>
    </div>
    <div class="cart">
      <h2>Shopping Cart</h2>
      <ul class="cart-list">
        <li v-for="(cartItem, index) in cart" :key="index">
          <div class="cart-item">
            <i :class="cartItem.icon"></i>
            <div class="product-details">
              <h3>{{ cartItem.title }}</h3>
              <p><strong>Location:</strong> {{ cartItem.Location }}</p>
              <p><strong>Price:</strong> {{ cartItem.price }}</p>
              <p><strong>Slots Available:</strong> {{ cartItem.spaces }}</p>
            </div>
            <button @click="removeFromCart(cartItem._id)" class="remove-button">Remove</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "CheckoutForm",
  props: ['cart','submit-order'],
  data() {
    return {
      order: {
        firstName: '',
        lastName: '',
        phone: ''
      },
      orderSubmitted: false
    };
  },
  computed: {
    isFormValid() {
      return this.order.firstName && this.order.lastName && this.order.phone;
    }
  },
  methods: {
    removeFromCart(id) {
      this.$emit('remove-from-cart', id);
    },
    submitOrderForm(){
      if (this.isFormValid) {
        this.$emit('submit-order',this.order)
      }
    }
  }
};
</script>

<style scoped>
/* Updated CSS for modern look */
.checkout-form {
  display: flex;
  justify-content: space-between;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.checkout,
.cart {
  width: 48%;
}

.checkout h2,
.cart h2 {
  text-align: center;
}

.checkout p,
.cart p {
  margin: 10px 0;
}

.checkout button,
.cart button {
  background-color: gold;
  color: #fff;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.checkout button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.cart-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.cart-item img {
  max-width: 100px;
  border-radius: 8px;
}

.product-details {
  flex: 1;
  margin-left: 20px;
}

.rating {
  display: flex;
  justify-content: center;
}
</style>
