<template>
  <h1>{{ json.length }}</h1>
    <table>
      <tr>
        <td>id</td>
        <td>title</td>
        <td>image</td>
      </tr>
      <tr v-if="loading.status">
        <td colspan="3" style="text-align:center; padding-top:20px;">{{loading.text}}</td> 
      </tr>
      <tr v-for="product in json" :key="product.id" v-else>
        <td>{{ product.id }}</td>
        <td>{{ product.name }}</td>
        <td><a :href="product.images[0].src" target="_blank">{{ product.images[0].src }}</a></td>
      </tr>
      <tr>
    <td colspan="3"><b>{{ total + ' products in store' }}</b></td> 
      </tr>
    </table>
    
</template>

<script>
import axios from 'axios';


export default {
  name: 'App',
  data() {
    return {
      url: 'https://poster4.me/wp-json/wc/v3/',
      json: [],
      consumerKey: 'ck_6c9b9b77d628d6718454229893e548fbf2cc3b16&',
      consumerSecret: 'cs_2c92e3e6ec90b0b9222eef0fb8fcdff76b432ed4',
      dataType: 'products',
      offset: 0,
      perPage: 5,
      total: 0,
      totalPages: 0,
      currentPage: 0,
      loading: {
        status: true,
        text: 'загрузка'
      }
      
    }
  },
  mounted() {
    this.getData(this.offset, this.perPage);
  },
  methods: {
    getData: function(offset, perPage) {
      axios({
        url: this.url + this.dataType + '?' + `consumer_key=${this.consumerKey}&consumer_secret=${this.consumerSecret}&offset=${offset}&per_page=${perPage}`,
      }).then((res) => {
        if (res.status == 200 && this.json.length == 0) {
          this.loading = false
          this.json = res.data
          this.total = res.headers["x-wp-total"]
          this.totalPages = res.headers["x-wp-totalpages"]
          console.log('first json', this.json)
          // localStorage.setItem('products', JSON.stringify(this.json))
        }
      }).catch((err) => {
        if (err) {
          console.log(err.message)
        }
      });
    },
    nextPage: function() {
      // this.getData(this.offset + 1, this.perPage)
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

table {
  text-align: left;
  width: 80%;
  margin: 0 auto;
  border-collapse: collapse;
}
td {
  padding: 10px 20px 10px 0px;
}
td:last-child {
  padding-right: 0px;
}
tr:first-child:hover,
tr:last-child:hover{
  background:none;
}
tr {
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  transition: all .3s ease;
}
tr:hover{
  background:rgba(0, 0, 0, 0.1);
}
</style>
