<template>
  <v-container>
    <h1>Listing Products</h1>
    <v-row justify="center">
      <v-col cols="12" sm="6" md="4">
        <v-card v-if="!isEditMode">
          <v-card-title>
            <h3>Add Product</h3>
          </v-card-title>
          <v-card-text>
            <v-form @submit.prevent="addProduct">
              <v-text-field
                v-model="product.title"
                label="Title"
                required
              ></v-text-field>
              <v-text-field
                v-model="product.price"
                label="Price"
                type="number"
                required
              ></v-text-field>
              <v-textarea
                v-model="product.description"
                label="Description"
                required
              ></v-textarea>
              <v-btn type="submit" color="primary">Add</v-btn>
            </v-form>
          </v-card-text>
        </v-card>
        <v-card v-else>
          <v-card-title>
            <h3>Edit Product</h3>
          </v-card-title>
          <v-card-text>
            <v-form @submit.prevent="updateProduct">
              <v-text-field
                v-model="updatedProduct.title"
                label="Title"
                required
              ></v-text-field>
              <v-text-field
                v-model="updatedProduct.description"
                label="Description"
                required
              ></v-text-field>
              <v-text-field
                v-model="updatedProduct.price"
                label="Price"
                type="number"
                required
              ></v-text-field>
              <v-btn type="submit" color="primary">Update</v-btn>
              <v-btn @click="cancelUpdate" color="secondary">Cancel</v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col
        cols="12"
        sm="6"
        md="4"
        v-for="product in products"
        :key="product.id"
      >
        <v-card>
          <v-card-title>
            <h3>{{ product.title }}</h3>
          </v-card-title>
          <v-card-text style="font-weight: 500">
            <div>Description: {{ product.description }}</div>
            <br />
            <div>Price: {{ product.price }}</div>
          </v-card-text>
          <v-card-actions>
            <v-btn @click="editProduct(product)" color="primary">Edit</v-btn>
            <v-btn @click="deleteProduct(product.id)" color="error"
              >Delete</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      product: {
        title: "",
        description: "",
        price: 0,
      },
      updatedProduct: {
        id: "",
        title: "",
        description: "",
        price: 0,
      },
      products: [],
      isEditMode: false,
    };
  },
  async mounted() {
    await this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      const response = await fetch("http://localhost:3000/products");
      const data = await response.json();
      this.products = data;
    },
    async addProduct() {
      const response = await fetch("http://localhost:3000/products", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.product),
      });
      if (response.ok) {
        await this.fetchProducts();
        this.product = {
          title: "",
          description: "",
          price: 0,
        };
      } else {
        console.error("Failed to add product.");
      }
    },
    async deleteProduct(productId) {
      const response = await fetch(
        `http://localhost:3000/products/${productId}`,

        {
          method: "DELETE",
        }
      );
      if (response.ok) {
        await this.fetchProducts();
        alert("You have successfully deleted the product!");
      } else {
        console.error("Failed to delete product.");
      }
    },
    editProduct(product) {
      this.isEditMode = true;
      this.updatedProduct = { ...product };
    },
    cancelUpdate() {
      this.isEditMode = false;
      this.updatedProduct = {
        id: "",
        title: "",
        description: "",
        price: 0,
      };
    },
    async updateProduct() {
      const response = await fetch(
        `http://localhost:3000/products/${this.updatedProduct.id}`,
        {
          method: "PATCH",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(this.updatedProduct),
        }
      );
      if (response.ok) {
        await this.fetchProducts();
        this.isEditMode = false;
        this.updatedProduct = {
          id: "",
          title: "",
          description: "",
          price: 0,
        };
        alert("The product has been successfully updated")
      } else {
        alert("Failed to update product.");
      }
    },
  },
};
</script>

<style>
.v-card {
  margin-bottom: 16px;
}
</style>
