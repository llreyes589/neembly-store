<script setup>
import axios from 'axios';
</script>

<template lang="">
  <div class="mt-3 mx-3">
    <div class="row">
      <div class="col-md-4 border">
        <form class="p-2">
          <h2>Add Product</h2>
          <hr />
          <div class="mb-3">
            <label for="title" class="form-label">Title</label>
            <input
              type="text"
              class="form-control"
              placeholder="Enter title"
              v-model="form.title"
            />
          </div>
          <div class="mb-3">
            <label for="price" class="form-label">Price</label>
            <input
              type="text"
              class="form-control"
              placeholder="Enter price"
              v-model="form.price"
            />
          </div>
          <div class="mb-3">
            <label for="description" class="form-label">Description</label>
            <textarea
              v-model="form.description"
              class="form-control"
              placeholder="Enter description"
              rows="3"
            ></textarea>
          </div>
          <div class="mb-3">
            <label for="" class="form-label">Category</label>
            <select class="form-select form-select-lg" v-model="form.category">
              <option
                :value="item"
                v-for="(item, index) in categories"
                :key="index"
              >
                {{ item }}
              </option>
            </select>
          </div>

          <div class="mb-3">
            <label for="image" class="form-label">Image</label>
            <input
              v-model="form.image"
              type="text"
              class="form-control"
              placeholder="Enter image url"
            />
          </div>

          <button
            class="btn"
            :class="$route.query.id ? 'btn-info' : 'btn-success'"
            type="button"
            @click="$route.query.id ? updateProduct() : saveProduct()"
          >
            {{ $route.query.id ? 'Update' : 'Save' }}
          </button>
        </form>
      </div>

      <div class="col-md">
        <div class="row mb-2">
          <div class="col-md-6">
            <div class="form-floating">
              <input
                type="text"
                class="form-control"
                placeholder="Search title here"
                autocomplete="off"
                v-model="text_search"
              />
              <label for="floatingPassword">Search title</label>
            </div>
          </div>
        </div>
        <div class="table-responsive">
          <table class="table table-primary">
            <thead>
              <tr>
                <th scope="col">id</th>
                <th scope="col">Title</th>
                <th scope="col">Price</th>
                <th scope="col">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr class="" v-for="item in products" :key="item.id">
                <td scope="row">{{ item.id }}</td>
                <td>{{ item.title }}</td>
                <td>{{ item.price }}</td>
                <td nowrap>
                  <a
                    :href="'?id=' + item.id"
                    class="btn btn-primary btn-sm"
                    type="button"
                  >
                    Edit / View
                  </a>
                  <button
                    class="btn btn-danger btn-sm"
                    type="button"
                    @click="deleteProduct"
                  >
                    Delete
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      api: 'https://fakestoreapi.com/products',
      form: {
        title: null,
        price: null,
        description: null,
        image: null,
        category: null,
      },
      products: [],
      categories: [
        "men's clothing",
        'jewelery',
        'electronics',
        "women's clothing",
      ],
      message: null,
      text_search: null,
    };
  },

  mounted() {
    this.init();
  },

  watch: {
    text_search(e) {
      if (e === '') this.init();
      this.products = this.products.filter(p => {
        const manipulatedTitle = p.title.toLowerCase();
        const manipulatedSearchQuery = e.toLowerCase();
        return manipulatedTitle.includes(manipulatedSearchQuery);
      });
    },
  },

  methods: {
    async init() {
      const { data } = await axios.get(this.api);
      this.products = data;

      if (this.$route.query.id) {
        const { data } = await axios.get(`${this.api}/${this.$route.query.id}`);
        this.form = data;
      }
    },

    async saveProduct() {
      const fd = new FormData();
      fd.append('title', this.form.title);
      fd.append('price', this.form.price);
      fd.append('description', this.form.description);
      fd.append('image', this.form.image);
      fd.append('category', this.form.category);

      try {
        const { data } = await axios.post(this.api, fd);

        alert(
          `Note: Posted data will not really insert into the database and just return a fake id. id:${data.id}`
        );
      } catch ({ response }) {
        alert(response.statusText);
      }
      // alert('saving');
    },
    async updateProduct() {
      const fd = new FormData();
      fd.append('title', this.form.title);
      fd.append('price', this.form.price);
      fd.append('description', this.form.description);
      fd.append('image', this.form.image);
      fd.append('category', this.form.category);

      try {
        const { data } = await axios.put(
          `${this.api}/${this.$route.query.id}`,
          fd
        );
        alert(
          `Note: PUT data will not really updated into the database and just return a fake id. id:${data.id}`
        );
      } catch ({ response }) {
        alert(response.statusText);
      }
      // alert('saving');
    },
    async deleteProduct() {
      try {
        const { data } = await axios.delete(
          `${this.api}/${this.$route.query.id}`
        );
        alert(
          `Note: DELETE data will not really delete into the database and just return a fake id. id:${data.id}`
        );
      } catch ({ response }) {
        alert(response.statusText);
      }
    },
  },
};
</script>
<style lang=""></style>
