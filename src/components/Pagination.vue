<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination d-flex">
      <li class="page-item">
        <button
          @click="prevPage"
          :disabled="currentPage === 1"
          class="page-link btn-sm"
        >
          Previous
        </button>
      </li>
      <li class="page-item d-flex">
        <button
          v-for="page in pages"
          :key="page"
          @click="changePage(page)"
          :class="[
            'btn-sm page-link',
            { 'bg-info text-white': page === currentPage },
          ]"
        >
          {{ page }}
        </button>
      </li>
      <li class="page-item">
        <button
          class="page-link btn-sm"
          @click="nextPage"
          :disabled="currentPage === totalPages"
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
      <h5 class="card-title">Total Price</h5>
      <h3 class="card-text">
        {{ getTotalPrice }} <i class="fa fa-dollar"></i>
      </h3>
    </div>
    <div class="card-body">
      <h5 class="card-title">Current Records</h5>
      <h3 class="card-text">
        {{ currentPageRecords }}
      </h3>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    items: {
      type: Array,
      required: true,
    },
    itemsPerPage: {
      type: Number,
      default: 10,
    },
  },
  data() {
    return {
      currentPage: 1,
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.items.length / this.itemsPerPage);
    },
    pages() {
      return Array.from({ length: this.totalPages }, (_, i) => i + 1);
    },
    totalRecords() {
      return this.items.length;
    },
    currentPageRecords() {
      return this.displayedItems.length;
    },
    getTotalPrice() {
      return this.items.reduce((total, item) => total + item.price, 0);
    },
    displayedItems() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.items.slice(startIndex, endIndex);
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
  },
};
</script>
