<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
      </div>
      <div class="search">
        <input type="text" class="form-control" placeholder="Search by title" :disabled="disabled" v-model="bookQuery.title" @keyup.enter="searchTitle"/>
      </div>
      <div class="content">
        <v-select :options="categories" label="list_name" v-model="bookQuery.bookCategory" @input="categoryChosen" placeholder="Choose a category" aria-placeholder="Choose a category"></v-select>
        <div id="bookGrid">
          <p id="numberResults"></p>
          <div v-for="result in bookQuery.results" :key="result.list_name" class="book">
            <a :href="`https://www.google.com/search?q=${result.title}+${result.author}`" target="_blank">
              <h5 class="title">{{result.title}}</h5> 
              <p class="author">by {{result.author}}</p>
              <p class="description">{{result.description}}</p>
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Books",
  data () {
    return {
      bookQuery: {
        bookCategory: "",
        title: "",
        results: []
      },
      categories: [],
      disabled: true,
    }
  },
  methods: {
    categoryChosen() {
      this.bookQuery.results.length = 0;
      if (this.bookQuery.bookCategory !== null && !!this.bookQuery.title) this.searchTitle();
      this.disabled = this.bookQuery.bookCategory === null;
      if (this.disabled) {
        this.bookQuery.title = "";
        document.getElementById("numberResults").innerHTML = ``;

      } 
    },
    searchTitle() {
      let query = this.bookQuery.bookCategory.replace(/\s+/g, '-').toLowerCase();
      fetch(`https://api.nytimes.com/svc/books/v3/lists/${query}.json?api-key=3HTG84FPJor0C9b72FFHwAFh1CI7baE6`)
      .then(response => response.json())      
      .then((data) => {
        this.bookQuery.results = data.results.books.filter(libro => libro.title.toLowerCase().includes(this.bookQuery.title.toLowerCase()));
        document.getElementById("numberResults").textContent = `Showing ${this.bookQuery.results.length} matching elements.`;
        }) 
      
      .catch(err => alert(err))
    },
  },

  
  created() {
  fetch('https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=3HTG84FPJor0C9b72FFHwAFh1CI7baE6')
    .then(response => response.json())
    .then((data) => 
      this.categories = data.results.slice(0,10).map((category) => category.list_name)
      )
    .catch(err => alert(err))
  },  
};
</script>
<style scoped lang="scss">
.container {
  z-index: 1;
  margin: 36px auto;
  max-width: 826px;
  background-color: white;
}

.card {
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
  padding: 24px;
}

.list {
  > li {
    &:not(:last-child) {
      margin-bottom: 18px;
    }
    > a {
      color: #0a5b8c;
      display: block;
      margin-bottom: 6px;
    }
    > span {
      color: rgba(#3b4242, 0.7);
      font-size: 12px;
    }
  }
}

.heading {
  margin-bottom: 12px;
  .title {
    font-size: 18px;
    font-weight: 600;
  }
}

.search {
  margin-bottom: 24px;
  .form-control {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #ced4da;
    border-radius: 0;
    outline: 0;
    box-shadow: none;
    padding: 6px 0;
    width: 100%;
  }
}

#bookGrid {
  display: inline-block;
  margin: 10px 10px 10px 10px;
  #numberResults {
    border-bottom: 2px solid #797d81;
  }
  .book {
    max-width: 826px;
    border-bottom: 1px solid #ced4da;
    margin: 10px 10px 10px 10px;
    a{
      text-decoration: none;
      text-align: start;
      color: rgb(46, 46, 46);
      .title {
        font-weight: bold;
        margin-bottom:2px
      }
      .author {
        font-size: 0.8rem;
        margin-bottom:1px;
      }
      .description {
        text-align:bottom;
      }
    }
  }
}


</style>
