<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
      </div>
      <div class="search">
        <input type="text" class="form-control" placeholder="Search by title" :disabled="disabled" v-model="bookQuery.bookTitle" @keyup.enter="searchTitle"/>
      </div>
      <div class="content">
        <v-select :options="categories" label="title" v-model="bookQuery.bookCategory" @input="categoryChosen"></v-select>
        <div v-for="result in bookQuery.results" :key="result.list_name" class="book">
          <h5> {{result.title}} </h5>
          <p> {{result.author}} </p>
          <p> {{result.description}} </p>
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
        bookTitle: "",
        results: []
      },
      categories: [],
      disabled: true,
    }
  },
  methods: {
    categoryChosen() {
      console.log(`you have chosen ${this.bookQuery.bookCategory})`)
      if (this.bookQuery.bookCategory !== null) this.disabled = false; //PONER MAS BELLO
      else {
        this.disabled = true;
        this.bookQuery.results.length = 0

        } 
      
    },
    searchTitle() {
      let query = this.bookQuery.bookCategory.replace(/\s+/g, '-').toLowerCase();
      console.log(query); 
      fetch(`https://api.nytimes.com/svc/books/v3/lists/${query}.json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j`)
      .then(response => response.json())      
      .then((data) => {
        this.bookQuery.results = data.results.books
        console.log(this.bookQuery.results[0].title)
        }) //TODOS los libros de esa categoria los pone en el array
      
      .catch(err => console.log(err))
      this.bookQuery.bookTitle =""
    },
  },

  
  created() {
  fetch('https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j')
    .then(response => response.json())
    .then((data) => 
      {for (let i = 0; i < 10; i++) {
        this.categories.push(data.results[i].list_name)  //MEJORAR PARA QUE USE SOLO SLICE
      }
    })
    .catch(err => console.log(err))
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

.btn {
  color: #fff;
  cursor: pointer;
  background-color: #117a8b;
  border: 1px solid transparent;
  margin: 1px;
  padding: 6px 12px;
  border-radius: 6px;
  transition: all 0.1s ease-in;
  &:hover {
    background-color: #138496;
    border-color: #117a8b;
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

.book {
  background: red;
  max-width: fit-content;
}

</style>
