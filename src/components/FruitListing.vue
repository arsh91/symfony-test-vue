<template>
  <!-- <div>
    <h1>Fruits</h1>
    <div v-if="loading">Loading...</div>
    <div v-else>
      <div>
        <label for="name">Filter by name:</label>
        <input type="text" id="name" v-model="filters.name">
      </div>
      <div>
        <label for="family">Filter by family:</label>
        <input type="text" id="family" v-model="filters.family">
      </div>
      <div v-if="fruits.length === 0">
        No fruits found
      </div>
      <div v-else>
        <div v-for="fruit in fruits" :key="fruit.id">
          <h3>{{ fruit.name }}</h3>
          <div>Family: {{ fruit.family }}</div>
          <div>Order: {{ fruit.order }}</div>
          <div>Genus: {{ fruit.genus }}</div>
          <div>
            Nutritions:
            <ul>
              <li>Calories: {{ fruit.calories }}</li>
              <li>Fat: {{ fruit.fat }}</li>
              <li>Sugar: {{ fruit.sugar }}</li>
              <li>Carbohydrates: {{ fruit.carbohydrates }}</li>
              <li>Protein: {{ fruit.protein }}</li>
            </ul>
          </div>
          <button @click="toggleFavorite(fruit.fruit_id)">
            {{ isFavorite(fruit.id) ? 'Remove from favorites' : 'Add to favorites' }}
          </button>
        </div>
        <div>
          <button v-if="page > 1" @click="loadPage(page - 1)">Previous page</button>
          <button v-if="page < totalPages" @click="loadPage(page + 1)">Next page</button>
        </div>
      </div>
    </div>
  </div> -->


    <div id="wrap m-4">
      <div class="container">
          <h3 class="mb-4">A demo of Listing all the Fruits</h3>
          <div v-if="loading">Loading...</div>
          <div v-else>
            <form @submit.prevent="submitFilter">
              <div class="mb-4">
                <label for="name">Filter by name:</label>
                <input type="text" class="form-control" id="name" v-model="filters.name">
              </div>
              <div class="mb-4">
                <label for="family">Filter by family:</label>
                <input type="text" class="form-control" id="family" v-model="filters.family">
              </div>
              <button type="submit" class="btn btn-primary mb-4">Apply Filters</button>
            </form>
              <table cellpadding="0" cellspacing="0" border="0" class="fruits_list_table table table-striped table-bordered" id="fruits_list_table">
                <thead>
                  <tr>
                    <th>Id</th>
                    <th>Name</th>
                    <th>Family</th>
                    <th>Order</th>
                    <th>Calories</th>
                    <th>Fat</th>
                    <th>Sugar</th>
                    <th>Carbohydrates</th>
                    <th>Protein</th>
                    <th>Favourite</th>
                  </tr>
                </thead>
                <tbody>
                    <tr v-if="fruits.length === 0" class="gradeX">
                      <td>No Record Found</td>
                    </tr>
                    <tr v-else v-for="fruit in fruits" :key="fruit.id" class="gradeX">
                        <td>{{ fruit.id }}</td>
                        <td>{{ fruit.name }}</td>
                        <td>{{ fruit.family }}</td>
                        <td>{{ fruit.order }}</td>
                        <td>{{ fruit.calories }}</td>
                        <td>{{ fruit.fat }}</td>
                        <td>{{ fruit.sugar }}</td>
                        <td>{{ fruit.carbohydrates }}</td>
                        <td>{{ fruit.protein }}</td>
                        <td>
                          <button v-if="!fruit.added" @click="addToCart(fruit.id)" class="btn btn-primary">Add </button>
                          <span v-else>Added to cart</span>
                        </td>
                    </tr>
                </tbody>
              </table>
              <div>
                <button @click="loadPage(page - 1)" :disabled="page === 1">Previous page</button>
                <button @click="loadPage(page + 1)" :disabled="page === totalPages">Next page</button>
              </div>
            </div>
        </div>
      </div>
    
</template>

<script>
import axios from 'axios'
import { API_URL } from '@/config.js';

export default {
  name: 'FruitListing',
  data() {
    return {
      loading: true,
      fruits: [],
      filters: {
        name: '',
        family: '',
      },
      page: 1,
      perPage: 10,
      totalPages: 1,
      favorites: [],
      url: API_URL, // Define URL globally
    }
  },
  methods: {
    loadPage(page) {
      this.page = page;
      this.loading = true
      axios.get(`${this.url}/fruits`, {
        params: {
          page,
          per_page: this.perPage,
          name: this.filters.name,
          family: this.filters.family,
        },
      }).then(response => {
        this.fruits = response.data.fruits
        this.totalPages = Math.ceil(response.data.totalCount / this.perPage)
        this.loading = false
        console.log('Total Pages', this.totalPages)
      }).catch(error => {
        console.log(error)
        this.loading = false
      })
    },
    addToCart(id) {
      // axios.post(`${this.url}/add-favorite-fruit`, null, {
      //   params: {
      //     fruit_id: id
      //   }
      // }).then(response => {
      //   if (response.data.success) {
      //       console.log('Item added successfully');
      //       // Change button text here
      //     } else {
      //       console.log('Error adding item');
      //     }
      //   this.fruits.find(fruit => fruit.id === id).added = true
      // }).catch(error => {
      //   console.log(error)
      // })
      if (this.favorites.includes(id)) {
        alert('Item already in favorites')
        // Change button text here
        return
      }
      axios.post(`${this.url}/add-favorite-fruit`, null, {
        params: {
          fruit_id: id
        }
      }).then(response => {
        if (response.data.success) {
          alert('Item added successfully');
          // Change button text here
          this.favorites.push(id)
        } else {
          alert('Favorite limit exceeded You will not insert more than 10 fruits in favourites')
        return
        }
      }).catch(error => {
        console.log(error)
      })
      
    },
    isFavorite(id) {
      return this.favorites.includes(id)
    },
    submitFilter() {
      this.loadPage(1);
    },
  },
  mounted() {
    axios.get(`${this.url}/favorite-fruits`).then(response => {
      this.favorites = response.data.fruits.map(item => item.fruitId)
    }).catch(error => {
      console.log(error)
    })
    this.loadPage(this.page)
  },
}
</script>