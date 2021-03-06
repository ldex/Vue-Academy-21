<template>
  <div>
    <h2>{{ title }}</h2>
    <fieldset class="filters">
      Sort by:
      <button @click="sort('name')">Name</button>
      <button @click="sort('price')">Price</button>
      <button @click="sort('modifiedDate')">Date</button>
      <span> Filter by name: <input v-model="filterName" /></span>
    </fieldset>
    <ul class="products">
      <li
        v-for="product in sortedFilteredPaginatedProducts"
        v-bind:key="product.id"
        :class="{
          discontinued: product.discontinued,
          selected: product === selectedProduct,
        }"
        @click="selectedProduct = product"
        :title="JSON.stringify(product)"
      >
        <span class="name">{{ product.name }}</span>
        <span class="description">{{ product.description }}</span>
        <span class="price">{{ product.price }}</span>
      </li>
    </ul>
    <button @click="prevPage" :disabled="pageNumber === 1">
      &lt; Previous
    </button>
    Page {{ pageNumber }}
    <button @click="nextPage" :disabled="pageNumber >= pageCount">
      Next &gt;
    </button>
    <product-details :product="selectedProduct"></product-details>
  </div>
</template>

<script>
import ProductDetails from "@/components/ProductDetails.vue";

export default {
  components: {
    ProductDetails,
  },
  props: {
    products: {
      type: Array,
      default: () => [],
    },
    pageSize: {
      type: Number,
      required: false,
      default: 5,
    },
  },
  data() {
    return {
      title: "Products",
      selectedProduct: null,
      filterName: "",
      sortName: "modifiedDate",
      sortDir: "desc",
      pageNumber: 1,
    };
  },
  methods: {
    sort(s) {
      //if s == current sort, reverse order
      if (s === this.sortName) {
        this.sortDir = this.sortDir === "asc" ? "desc" : "asc";
      }
      this.sortName = s;
    },
    nextPage() {
      this.pageNumber++;
      this.selectedProduct = null;
    },
    prevPage() {
      this.pageNumber--;
      this.selectedProduct = null;
    },
  },
  watch: {
    // reset pagination when filtering
    filterName() {
      this.pageNumber = 1;
    },
    // reset pagination when sorting
    sortName() {
      this.pageNumber = 1;
    },
    sortDir() {
      this.pageNumber = 1;
    },
  },
  computed: {
    filteredProducts() {
      let filter = new RegExp(this.filterName, "i");
      return this.products.filter((el) => el.name.match(filter));
    },
    sortedFilteredProducts() {
      return [...this.filteredProducts].sort((a, b) => {
        let modifier = 1;
        if (this.sortDir === "desc") modifier = -1;
        if (a[this.sortName] < b[this.sortName]) return -1 * modifier;
        if (a[this.sortName] > b[this.sortName]) return 1 * modifier;
        return 0;
      });
    },
    sortedFilteredPaginatedProducts() {
      const start = (this.pageNumber - 1) * this.pageSize,
        end = start + this.pageSize;

      return this.sortedFilteredProducts.slice(start, end);
    },
    pageCount() {
      let l = this.filteredProducts.length,
        s = this.pageSize;
      return Math.ceil(l / s);
    },
  },
};
</script>

<style lang="css" scoped>
.filters {
  padding: 10px;
}
.products {
  margin: 0;
  list-style-type: none;
  padding: 0;
}
.products li {
  cursor: pointer;
  position: relative;
  left: 0;
  background-color: #eee;
  margin: 0.5em;
  padding: 0.3em 0em;
  height: 1.8em;
  border-radius: 4px;
}
.products li:hover {
  color: #607d8b;
  background-color: yellow;
  left: 0.1em;
}
.products li:hover .name,
.products li:hover .price {
  color: #607d8b;
  background-color: #ffd800;
  left: 0.1em;
}
.products li.selected {
  background-color: #0094ff;
  color: white;
}
.products li.selected:hover {
  color: white;
}
.products li.selected .name,
.products li.selected .price {
  background-color: #0026ff;
  color: white;
}
.products .text {
  padding: 0.5em 0.7em 0em 0.7em;
  line-height: 1em;
  left: -1px;
  top: -4px;
  height: 1.8em;
}
.products .name {
  display: inline-block;
  color: white;
  padding: 0.5em 0.7em 0em 0.7em;
  background-color: #607d8b;
  line-height: 1em;
  position: relative;
  left: -1px;
  top: -4px;
  height: 1.8em;
  margin-right: 0.8em;
  border-radius: 4px 0px 0px 4px;
  width: 30%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.products .price {
  float: right;
  width: 15%;
  color: white;
  padding: 0.5em 0.7em 0em 0.7em;
  background-color: #607d8b;
  line-height: 1em;
  position: relative;
  left: -1px;
  top: -4px;
  height: 1.8em;
  margin-left: 0.8em;
  border-radius: 0px 4px 4px 0px;
}
.products .description {
  height: 1.8em;
  display: inline-block;
  width: 40%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.products .discontinued,
.products .discontinued * {
  color: red !important;
}
</style>