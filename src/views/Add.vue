<template>
  <div class="container mt-4">
    <form
      class="needs-validation"
      novalidate
      enctype="multipart/form-data"
      @submit.prevent="onSubmit()"
    >
      <div class="row">
        <div class="col-md-4 mb-3">
          <div class="row">
            <div class="col-md-12 mb-3">
              <div class="form-group">
                <label for="image" class="form-label">Product Image</label>
                <input
                  type="file"
                  class="form-control"
                  id="image"
                  required
                  accept="image/*"
                  @change="imageUpload(this)"
                />
                <label
                  for="image"
                  class="error text-danger"
                  v-if="errors['image']"
                  >{{ errors["image"] }}</label
                >
              </div>
            </div>
            <div class="col-md-12 mb-3">
              <img
                class=""
                v-if="product.image"
                :src="product.image"
                width="200"
                height="200"
              />
            </div>
          </div>
        </div>
        <div class="col-md-8">
          <div class="row">
            <div class="col-md-6 mb-3">
              <div class="form-group">
                <label for="title" class="form-label">Product Title</label>
                <input
                  type="text"
                  class="form-control"
                  id="title"
                  required
                  v-model="product.title"
                  @blur="validated()"
                />
                <label
                  for="title"
                  class="error text-danger"
                  v-if="errors['title']"
                  >{{ errors["title"] }}</label
                >
              </div>
            </div>

            <div class="col-md-6 mb-3">
              <div class="form-group">
                <label for="price" class="form-label">Product Price</label>
                <input
                  type="number"
                  class="form-control"
                  id="price"
                  step="0.01"
                  required
                  v-model="product.price"
                  @blur="validated()"
                />
                <label
                  for="price"
                  class="error text-danger"
                  v-if="errors['price']"
                  >{{ errors["price"] }}</label
                >
              </div>
            </div>

            <div class="col-md-6 mb-3">
              <div class="form-group">
                <label for="description" class="form-label"
                  >Product Description</label
                >
                <textarea
                  class="form-control"
                  id="description"
                  rows="3"
                  required
                  v-model="product.description"
                  @blur="validated()"
                ></textarea>
                <label
                  for="description"
                  class="error text-danger"
                  v-if="errors['description']"
                  >{{ errors["description"] }}</label
                >
              </div>
            </div>

            <div class="col-md-6 mb-3">
              <div class="form-group">
                <label for="category" class="form-label"
                  >Product Category</label
                >
                <input
                  v-if="!isEdit"
                  type="text"
                  class="form-control"
                  id="category"
                  v-model="product.category"
                  @blur="validated()"
                  required
                />
                <select
                  v-else
                  class="form-control"
                  v-model="product.category"
                  required
                  @change="validated()"
                  id="category"
                >
                  <option v-for="category in categories" :value="category">
                    {{ category }}
                  </option>
                </select>
                <label
                  for="category"
                  class="error text-danger"
                  v-if="errors['category']"
                  >{{ errors["category"] }}</label
                >
              </div>
            </div>

            <div class="col-md-6 mb-3">
              <div class="form-group">
                <label for="rating" class="form-label">Rating</label>
                <input
                  type="number"
                  class="form-control"
                  id="rating"
                  min="0"
                  max="5"
                  step="0.1"
                  required
                  v-model="product.rating.rate"
                  @blur="validated()"
                />
                <label
                  for="rating"
                  class="error text-danger"
                  v-if="errors['rating']"
                  >{{ errors["rating"] }}</label
                >
              </div>
            </div>
            <div class="col-md-6 mb-3">
              <div class="form-group">
                <label for="count" class="form-label">Total Reachouts</label>
                <input
                  type="number"
                  class="form-control"
                  id="rating"
                  min="0"
                  step="0"
                  required
                  v-model="product.rating.count"
                  @blur="validated()"
                />
                <label
                  for="count"
                  class="error text-danger"
                  v-if="errors['count']"
                  >{{ errors["count"] }}</label
                >
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row d-flex justify-content-center">
        <div class="col-md-4">
          <button type="submit" class="btn btn-primary">Submit</button>
        </div>
      </div>
    </form>
  </div>
</template>
<script>
export default {
  name: "AddView",
  data() {
    return {
      isEdit: false,
      previewImg: "",
      errors: [],
      categories: this.getLocalCategories() || [],
      product: {
        id: "",
        title: "",
        price: "",
        description: "",
        category: "",
        image: "",
        rating: {
          rate: "",
          count: "",
        },
      },
    };
  },
  methods: {
    resetProduct() {
      this.product = {
        title: "",
        price: "",
        description: "",
        category: "",
        rating: {
          rate: "",
          count: "",
        },
        image: "",
      };
    },
    onSubmit() {
      if (this.validated()) {
        const productList = this.getLocalProducts() || [];
        const product = {
          id: productList.length + 1,
          ...this.product,
        };
        productList.push(product);
        if (!this.checkCategoryExist(product.category)) {
          this.addCategory(product.category);
        }
        this.setLocalProducts(productList);
        this.resetProduct();
        this.$router.push({ name: "home" });
      }
    },
    validated: function () {
      if (
        this.product.image &&
        this.product.title &&
        this.product.price &&
        this.product.description &&
        this.product.category &&
        this.product.rating.rate &&
        this.product.rating.count
      ) {
        return true;
      }

      this.errors = [];
      if (!this.product.title.trim().length) {
        this.errors["title"] = "Title required.";
      }
      if (!this.product.price) {
        this.errors["price"] = "Price required.";
      } else if (this.product.price < 0) {
        this.errors["price"] = "Price must be greater than 0.";
      }
      if (!this.product.description.trim().length) {
        this.errors["description"] = "Description required.";
      }
      if (!this.product.category.trim().length) {
        this.errors["category"] = "Category required.";
      }

      if (!this.product.rating.rate) {
        this.errors["rating"] = "Rating required.";
      } else if (this.product.rating.rate < 0 || this.product.rating.rate > 5) {
        this.errors["rating"] = "Rating must be between 0 and 5.";
      }
      if (!this.product.rating.count) {
        this.errors["count"] = "Count required.";
      } else if (this.product.rating.count < 0) {
        this.errors["count"] = "Count must be greater than 0.";
      }
      if (!this.product.image) {
        this.errors["image"] = "Image required.";
      }
    },
    getSpecificProduct(id) {
      const productList = this.getLocalProducts() || [];

      return productList.find((product) => product.id == id);
    },
    getLocalCategories() {
      return JSON.parse(localStorage.getItem("category_list"));
    },
    getLocalProducts() {
      return JSON.parse(localStorage.getItem("product_list"));
    },
    setLocalProducts(newProductList) {
      return localStorage.setItem(
        "product_list",
        JSON.stringify(newProductList)
      );
    },
    checkCategoryExist(category) {
      const categoryList = this.getLocalCategories() || [];
      if (categoryList.includes(category.trim())) {
        return true;
      }
      return false;
    },
    addCategory(category) {
      const categoryList = this.getLocalCategories() || [];
      categoryList.push(category);
      this.setCategory(categoryList);
    },
    setCategory(newCategory) {
      return localStorage.setItem("category_list", JSON.stringify(newCategory));
    },
    imageUpload() {
      this.product.image = null;
      const file = document.getElementById("image").files[0];
      if (!file.type.match("image.*")) {
        this.errors["image"] = "Please select an image file";
        return;
      }
      this.product.image = URL.createObjectURL(file);
    },
    onSubmit() {
      if (!this.validated()) {
        return false;
      }
      if (this.isEdit) {
        const newProductList = this.getLocalProducts() || [];
        const index = newProductList.findIndex(
          (product) => product.id == this.product.id
        );
        newProductList[index] = this.product;
        this.setLocalProducts(newProductList);
        this.$router.push({ name: "home" });
      } else {
        const newProductList = this.getLocalProducts() || [];
        if (newProductList.length > 0) {
          this.product.id = newProductList[0].id + 1;
          newProductList.unshift(this.product);
        } else {
          this.product.id = 1;
          newProductList.push(this.product);
        }
        if (!this.checkCategoryExist(this.product.category)) {
          this.addCategory(this.product.category);
        }
        this.setLocalProducts(newProductList);
        this.$router.push({ name: "home" });
      }
    },
  },
  watch: {
    "$route.params.id": {
      handler(newVal) {
        if (!newVal) {
          this.isEdit = false;
          this.resetProduct();
        } else {
          this.isEdit = true;
          this.product = this.getSpecificProduct(newVal);
        }
      },
      immediate: true,
    },
  },
  mounted() {
    if (this.$route.params.id) {
      this.isEdit = true;
      const productID = this.$route.params.id;
      this.product = this.getSpecificProduct(productID);
    }
  },
};
</script>
