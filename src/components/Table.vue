<template>
    <div class="container">
        <div class="search">
            <label for="tableSearch">Поиск:</label>
            <input type="text" v-model="search" placeholder="Search ..." name="tableSearch"/>
        </div>
        <table>
            <thead>
                <tr>
                    <th @click="sort('id')">id</th>
                    <th @click="sort('email')">name</th>
                    <th @click="sort('name')">email</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in sortedPosts" :key="post.name">
                    <td>{{post.id}}</td>
                    <td>{{post.name}}</td>
                    <td>{{post.email}}</td>
                </tr>
            </tbody>
        </table>
        <p>
  <button @click="prevPage">Previous</button> 
  <button @click="nextPage">Next</button>
  </p>
    </div>
</template>

<script>
export default {
    data() {
        return {
            posts: [],
            search: '',
            currentSort:'name',
            currentSortDir:'asc',
            pageSize: 10,
            currentPage: 1,
            pages: []
        }
    },
    created() {
    fetch('https://jsonplaceholder.typicode.com/comments')
    .then(res => res.json())
    .then(res => {
      this.posts = res;
    })
    },
    computed: {
    sortedPosts() {
      let obj = this.posts;
      let newArray = [];
      const srch = (this.search.toLowerCase());
      for (let key in obj) {
        let el = obj[key];
        if (el.id == srch || el.name.toLowerCase().indexOf(srch) != -1 || el.email.toLowerCase().indexOf(srch) != -1)  {
            newArray.push(el);
        }
      }   
      return newArray.slice().sort((a,b) => {
        let modifier = 1;
        if(this.currentSortDir === 'desc') modifier = -1;
        if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
        if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        return 0;
      }).filter((row, index) => {
        let start = (this.currentPage-1)*this.pageSize;
        let end = this.currentPage*this.pageSize;
        if(index >= start && index < end) return true;
      });
    },

  },
  methods: {
    sort:function(s) {
      if(s === this.currentSort) {
        this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
      }
      this.currentSort = s;
    },
    nextPage:function() {
      if((this.currentPage*this.pageSize) < this.posts.length) this.currentPage++;
    },
    prevPage:function() {
      if(this.currentPage > 1) this.currentPage--;
    }
  }
}
</script>

<style scoped>
    .container {
        margin: 0 auto;
        width: 930px;
    };
    input {
        margin-left: 10px;
    }
    th {
        color: black;
        background: grey;
        cursor: pointer;
    };
    td:nth-child(1) {
        width: 139px;
    };
    td:nth-child(2) {
        width: 465px;
    }
    td:nth-child(3) {
        width: 325px;
    }
    

</style>