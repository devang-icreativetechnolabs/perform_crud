<template>
  <div>
    <div class="row p-2 d-flex align-items-center">
      <div class="col-md-3">
        <div class="form-group">
          <label for="categoryFilter">Category</label>
          <select
            id="categoryFilter"
            class="form-control"
            v-model="selectedCategory"
            @change="applyFilter"
          >
            <option value="">All</option>
            <option v-for="category in categories" :key="category">
              {{ category }}
            </option>
          </select>
        </div>
      </div>
      <div class="col-md-3">
        <div class="form-group">
          <label for="nameFilter">Name</label>
          <input
            type="text"
            id="nameFilter"
            class="form-control search"
            v-model="nameFilter"
            @input="applyFilter"
            @blur="
              nameFilter = nameFilter.trim();
              applyFilter();
            "
          />
        </div>
      </div>
      <div class="col-md-3">
        <button type="button" class="btn btn-primary" @click="resetFilter()">
          Clear Filter
        </button>
      </div>
    </div>
    <table class="table table-hover">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Image</th>
          <th scope="col">Title</th>
          <th scope="col">Price</th>
          <th scope="col">Description</th>
          <th scope="col">Category</th>
          <th scope="col">Rating</th>
          <th scope="col">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(product, index) in currentPageProducts" :key="product.id">
          <th scope="row">{{ index + 1 + (currentPage - 1) * limit }}</th>
          <td><img :src="product.image" width="100" height="100" /></td>
          <td scope="row" class="col-md-2">{{ product.title }}</td>
          <td scope="row">
            <span class="bg-dark rounded-pill p-2 text-warning fw-bold">
              {{ product.price }}<i class="fa fa-dollar"></i>
            </span>
          </td>
          <td class="col-md-2">
            {{
              product.description.length > 100
                ? product.description.slice(0, 100) + "..."
                : product.description
            }}
          </td>
          <td>
            <span class="badge rounded-pill p-2 bg-primary">{{
              product.category
            }}</span>
          </td>
          <td>
            <span class="bg-dark text-white p-2 rounded-pill">
              {{ product.rating.rate }}
              <i class="fa fa-star text-warning"></i> by
              {{ product.rating.count ?? 0 }}
              <i class="fa fa-users text-info"></i>
            </span>
          </td>
          <td>
            <div class="d-flex gap-2">
              <button
                type="button"
                class="btn btn-danger btn-sm"
                @click="openConfirmation(product.id)"
              >
                Delete
              </button>
              <router-link
                class="btn btn-info btn-sm"
                :to="{ name: 'edit', params: { id: product.id } }"
                >Edit</router-link
              >
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <div>
      <nav aria-label="Page navigation example">
        <ul class="pagination d-flex justify-content-end">
          <li class="page-item">
            <button
              type="button"
              @click="prevPage"
              :disabled="currentPage === 1"
              class="page-link"
              :class="[{ 'bg-secondary text-white': currentPage === 1 }]"
            >
              Previous
            </button>
          </li>
          <li class="page-item d-flex">
            <button
              type="button"
              v-for="page in pages"
              :key="page"
              @click="changePage(page)"
              :class="[
                'page-link',
                { 'bg-primary text-white': page === currentPage },
              ]"
            >
              {{ page }}
            </button>
          </li>
          <li class="page-item">
            <button
              type="button"
              class="page-link"
              @click="nextPage"
              :disabled="currentPage === totalPages"
              :class="[
                { 'bg-secondary text-white': currentPage === totalPages },
              ]"
            >
              Next
            </button>
          </li>
        </ul>
      </nav>
      <div class="d-flex justify-content-between">
        <div class="card-body">
          <h5 class="card-title">Total Records</h5>
          <h3 class="card-text">
            {{ totalRecords }}
          </h3>
        </div>
        <div class="card-body">
          <h5 class="card-title">Total Current Page Price</h5>
          <h3 class="card-text">
            {{ getTotalPrice }} <i class="fa fa-dollar"></i>
          </h3>
        </div>
        <div class="card-body">
          <h5 class="card-title">Current Page Records</h5>
          <h3 class="card-text">{{ currentPageRecords }}</h3>
        </div>
      </div>
    </div>
  </div>
  <ConfirmationModal
    :show="showConfirm"
    :message="confirmMessage"
    :id="selectedId"
    @confirm="deleteProduct"
    @cancel="showConfirm = false"
  ></ConfirmationModal>
</template>
<script>
import ConfirmationModal from "../components/ConfirmationModal.vue";
export default {
  name: "HomeView",
  components: {
    ConfirmationModal,
  },
  data() {
    return {
      url: "https://fakestoreapi.com/products",
      totalProducts: [],
      categories: [],
      currentPage: 1,
      limit: 5,
      selectedCategory: "",
      nameFilter: "",
      confirmMessage: "You want to delete this product?",
      showConfirm: false,
      selectedId: null,
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.totalProducts.length / this.limit);
    },
    pages() {
      return Array.from({ length: this.totalPages }, (_, i) => i + 1);
    },
    totalRecords() {
      return this.totalProducts.length;
    },
    currentPageRecords() {
      return this.currentPageProducts.length || 0;
    },
    getTotalPrice() {
      return this.currentPageProducts
        .reduce((total, item) => total + item.price, 0)
        .toFixed(2);
    },
    currentPageProducts() {
      const startIndex = (this.currentPage - 1) * this.limit;
      const endIndex = startIndex + this.limit;
      return this.totalProducts.slice(startIndex, endIndex);
    },
  },
  methods: {
    changePage(page) {
      this.currentPage = page;
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    async getproducts() {
      const res = await fetch(this.url);
      const data = await res.json();
      const reversedData = data.reverse();
      localStorage.setItem("product_list", JSON.stringify(reversedData));
      return data;
    },
    async getCategories() {
      const res = await fetch(`${this.url}/categories`);
      const data = await res.json();
      localStorage.setItem("category_list", JSON.stringify(data));
      return reversedData;
    },
    getLocalCategories() {
      return JSON.parse(localStorage.getItem("category_list"));
    },
    getLocalProducts() {
      return JSON.parse(localStorage.getItem("product_list"));
    },
    resetPagination() {
      this.currentPage = 1;
      this.limit = 5;
    },
    resetFilter() {
      this.selectedCategory = "";
      this.nameFilter = "";
      this.applyFilter();
    },
    async applyFilter() {
      this.totalProducts = await this.getLocalProducts();
      if (this.selectedCategory || this.nameFilter) {
        this.resetPagination();
        if (this.selectedCategory) {
          this.totalProducts = this.totalProducts.filter(
            (product) => product.category === this.selectedCategory
          );
        }
        if (this.nameFilter) {
          this.totalProducts = this.totalProducts.filter((product) =>
            product.title.toLowerCase().includes(this.nameFilter.toLowerCase())
          );
        }
      }
    },
    openConfirmation(productId) {
      this.showConfirm = true;
      this.confirmMessage = "You want to delete this product?";
      this.selectedId = productId;
    },
    async deleteProduct(productId) {
      const filteredProducts = this.totalProducts.filter(
        (product) => product.id !== productId
      );
      localStorage.setItem("product_list", JSON.stringify(filteredProducts));
      this.applyFilter();
      this.showConfirm = false;
    },
  },

  async created() {
    if (localStorage.getItem("category_list")) {
      this.categories = this.getLocalCategories();
    } else {
      this.categories = await this.getCategories();
    }
    if (localStorage.getItem("product_list")) {
      this.totalProducts = this.getLocalProducts();
    } else {
      this.totalProducts = await this.getproducts();
    }
  },
};
</script>
