<template>
  <div id="app">
    <header>
      <h1>After School Classes</h1>
      <button @click="showCheckout = !showCheckout">
        {{ showCheckout ? 'Back to Lessons' : 'View Cart' }} ({{ cart.length }})
      </button>
    </header>

    <LessonComponent v-if="!showCheckout" :canAddToCart="canAddToCart" :cartCount="cartCount"
      @add-to-cart="addToCart" />
    <CheckoutForm v-else :cart="cart" @remove-from-cart="removeFromCart" @submit-order="submitOrder" />
  </div>
</template>

<script>
import LessonComponent from './components/LessonComponent.vue';
import CheckoutForm from './components/CheckoutForm.vue';

export default {
  name: 'App',
  components: {
    LessonComponent,
    CheckoutForm
  },
  data() {
    return {
      lessons: [],
      cart: [],
      showCheckout: false,
      baseURL: "https://cw2-backend-lyart.vercel.app/"
    };
  },
  methods: {
    async submitOrder(order) {
      
        try {
          // Update lessons
          this.cart.forEach((item) => {
            fetch(`${this.baseURL}collection/lessons/${item._id}`, {
              method: "PUT",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify({ spaces: item.spaces-this.cart.filter(i => i._id === item._id).length}),
            });
          });

          const response = await fetch(`${this.baseURL}collection/orders`, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({
              ...order,
              cart: this.cart
            })
          });
          if (response.ok) {
            this.cart.length = 0
            this.showCheckout = false
            alert('Order Submitted')
          }
        } catch (error) {
          console.error('Error submitting order:', error);
        }
    },
    
    canAddToCart(lesson) {
      return lesson.spaces > this.cartCount(lesson._id);
    },
    cartCount(lessonId) {
      return this.cart.filter(item => item._id === lessonId).length;
    },
    addToCart(lesson) {
      if (this.canAddToCart(lesson)) {
        this.cart.push(lesson);
      }
    },
    removeFromCart(id) {
      const index = this.cart.findIndex(item => item._id === id);
      if (index !== -1) {
        this.cart.splice(index, 1);
      }
    }
  }
};
</script>

<style scoped>
/* Updated CSS for modern look */
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: #f5f5f5;
  border-bottom: 1px solid #ddd;
}

header h1 {
  margin: 0;
}

header button {
  background-color: gold;
  color: #fff;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}
</style>
