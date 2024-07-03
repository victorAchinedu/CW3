<template>
  <div>
    <div class="d-flex align-items-end gap-3 flex-wrap mb-3">

<div>
  <label for="search" class="form-label">Search</label>
  <input id="search" type="text" class="form-control" v-model="searchTerm" placeholder="search for a lesson..."
    v-on:input="searchLessons">
</div>


<div>
  <label for="sortAttribute" class="form-label">Sort By:</label>
  <select v-model="sortAttribute" class="form-select" v-on:change="searchLessons">
    <option value="title">Title</option>
    <option value="subject">Subject</option>
    <option value="location">Location</option>
    <option value="price">Price</option>
    <option value="spaces">Spaces</option>
  </select>
</div>

<div>
  <label for="sortOrder" class="form-label">Sort Order:</label>
  <select v-model="sortOrder" class="form-select" v-on:change="searchLessons">
    <option value="asc">Ascending</option>
    <option value="desc">Descending</option>
  </select>
</div>

</div>

    <main id="main-wrapper">
      <div class="product-container">
        <div class="product" v-for="(lesson, index) in lessons" :key="index">
          <i :class="lesson.icon"></i>
          <div class="lesson-details">
            <h2>{{ lesson.title }}</h2>
            <p>Location: {{ lesson.Location }}</p>
            <p>Price: {{ lesson.price }}</p>
            <p>Slots Available: {{ lesson.spaces }}</p>
            <button class="success-button" @click="addLessonToCart(lesson)" :disabled="lesson.availableInventory === 0">Add to Cart</button>
            <span v-if="lesson.availableInventory === 0">All out!</span>
            <span v-else-if="lesson.availableInventory < 5">Only {{ lesson.availableInventory - cartCount(lesson._id) }} left!</span>
            <span v-else>Buy Now!</span>

          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "LessonComponent",
  props: [
    "canAddToCart",
    "cartCount","add-to-cart"],
  data() {
    return {
      lessons: [],
      searchTerm: '',
      sortAttribute: 'subject',
        sortOrder: 'asc',
        baseURL: "https://cw2-backend-lyart.vercel.app/"
    };
  },
  created() {
    this.fetchLessons();
  },
  methods: {
    async fetchLessons() {
      try {
        const response = await fetch(`${this.baseURL}collection/lessons`);
        this.lessons = await response.json();
      } catch (error) {
        console.error("Error fetching lessons:", error);
      }
    },
    async searchLessons() { //filter lessons from backend
      var query = `?search=${this.searchTerm}&sort=${this.sortAttribute}&order=${this.sortOrder}`
      var res = await fetch(`${this.baseURL}search/collection/lessons/${query}`);
      this.lessons = await res.json();
    },
    addLessonToCart(lesson) {
      this.$emit("add-to-cart", lesson);
    },
  },
};
</script>

<style scoped>
/* Updated CSS for modern look */
.product-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
}

.product {
  background: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin: 10px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  width: calc(33.333% - 20px);
  box-sizing: border-box;
}

.product img {
  max-width: 100%;
  border-radius: 8px;
}

.lesson-details {
  text-align: center;
}

.success-button {
  background-color: gold;
  color: #fff;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.success-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

@media (max-width:575.98px) {
  .product {
    width: 100%;
  }
}
</style>
