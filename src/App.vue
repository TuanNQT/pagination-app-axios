<!-- using vue-jw-pagination
<template>
  <div>
    <h1 style="text-align: center">List Comment</h1>
    <table>
      <tr>
        <td>PostID</td>
        <td>Name</td>
        <td>Email</td>
        <td>Body</td>
      </tr>
      <tr v-for="post in pageOfItems" v-bind:key="post.id">
        <td>{{ post.postId }}</td>
        <td>{{ post.name }}</td>
        <td>{{ post.email }}</td>
        <td>{{ post.body }}</td>
      </tr>
    </table>
    <div class="card-footer pb-0 pt-3">
      <jw-pagination
        :items="listallitem"
        @changePage="onChangePage"
        :pageSize=5
        :maxPages="10"
      ></jw-pagination>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Vue from "vue";
import VueAxios from "vue-axios";
Vue.use(VueAxios, axios);
export default {
  name: "CommentList",
  data() {
    return { listallitem: [], pageOfItems: [] };
  },
  mounted() {
    Vue.axios
      .get("http://jsonplaceholder.typicode.com/comments")
      .then((resp) => {
        this.listallitem = resp.data;
        console.log(resp.data);
      });
  },
  methods: {
    onChangePage(pageOfItems) {
      // update page of items
      this.pageOfItems = pageOfItems;
    },
  },
};
</script>
<style scoped>
body {
  background: #20262e;
  padding: 20px;
  font-family: Helvetica;
}
table {
  margin: 20px 5px 0px 45px;
}

li {
  margin: 8px 0;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

del {
  color: rgba(0, 0, 0, 0.3);
}

li.inline {
  display: inline;
}
</style> -->
<!--  thủ công -->
<template>
  <div class="col-sm-12">
    <div class="offset">
      <table class="table table-bordered">
        <thead>
          <tr>
            <th>Name</th>
            <th>Email Name</th>
            <th>Body</th>
            <th>Post ID</th>
            <th>ID</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="p in displayedPosts" v-bind:key="p.id">
            <td>{{ p.name }}</td>
            <td>{{ p.email }}</td>
            <td>{{ p.body }}</td>
            <td>{{ p.postId }}</td>
            <td>{{ p.id }}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td>
              <input
                type="text"
                v-model="objPost.name"
                placeholder="Input Name"
                required
              />
            </td>
            <td>
              <input
                type="text"
                v-model="objPost.email"
                placeholder="Input Email"
                required
              />
            </td>
            <td>
              <input
                type="text"
                v-model="objPost.body"
                placeholder="Input Body"
                required
              />
            </td>
            <td>
              <input
                type="number"
                v-model="objPost.postId"
                placeholder="Post ID"
                required
              />
            </td>
          </tr>
          <tr>
            <button v-on:click="addPost()" class="btn-primary">Add Post</button>
          </tr>
        </tfoot>
      </table>
      <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li class="page-item">
            <button
              type="button"
              class="page-link"
              v-if="page != 1"
              @click="page--"
            >
              Previous
            </button>
            <button type="button" class="page-link" @click="page = 1">
              Last Page
            </button>
          </li>
          <li class="page-item">
            <button
              type="button"
              class="page-link"
              v-for="pageNumber in pages.slice(
                page > 1 ? page - 2 : page - 1,
                page + 5
              )"
              v-bind:key="pageNumber.index"
              @click="page = pageNumber"
              :style="[
                pageNumber == page ? { color: 'blue' } : { color: '#29b3ed' },
              ]"
            >
              {{ pageNumber }}
            </button>
          </li>
          <li class="page-item">
            <button
              type="button"
              @click="page++"
              v-if="page < pages.length"
              class="page-link"
            >
              Next
            </button>
            <button
              type="button"
              class="page-link"
              @click="page = pages.length"
            >
              End Page
            </button>
          </li>
        </ul>
      </nav>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Vue from "vue";
import VueAxios from "vue-axios";
Vue.use(VueAxios, axios);
export default {
  name: "CommentList",
  data() {
    return {
      objPost: { postId: "", name: "", email: "", body: "" },
      posts: [""],
      page: 1,
      perPage: 9,
      pages: [],
    };
  },
  methods: {
    getPosts() {
      axios.get("http://jsonplaceholder.typicode.com/comments").then((resp) => {
        if (localStorage.posts == undefined) {
          localStorage.setItem("posts", JSON.stringify(resp.data)); //Save LocaStorage
        }
        this.posts = JSON.parse(localStorage.getItem("posts")); // gán post = LocalStorage
        this.sort();
        console.log(resp.data);
      });
    },
    setPages() {
      let numberOfPages = Math.ceil(this.posts.length / this.perPage);
      this.pages.length = 0;
      for (let index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
      }
    },
    paginate(posts) {
      let page = this.page;
      let perPage = this.perPage;
      let from = page * perPage - perPage;
      let to = page * perPage;
      return posts.slice(from, to);
    },
    sort() {
      this.posts.sort((a, b) => b.id - a.id);
    },
    addPost() {
      this.objPost.id = this.posts.length + 1;
      var objAdd = {
        postId: parseInt(this.objPost.postId),
        id: this.objPost.id,
        name: this.objPost.name,
        email: this.objPost.email,
        body: this.objPost.body,
      };
      this.posts.push(objAdd);
      localStorage.setItem("posts", JSON.stringify(this.posts));
      this.sort();
      this.objPost.name = "";
      this.objPost.email = "";
      this.objPost.body = "";
      this.objPost.postId = "";
      this.setPages();
    },
  },
  computed: {
    displayedPosts() {
      return this.paginate(this.posts);
    },
  },
  watch: {
    posts() {
      this.setPages();
    },
  },
  created() {
    this.getPosts();
  },
  filters: {
    trimWords(value) {
      return value.split(" ").splice(0, 20).join(" ") + "...";
    },
  },
};
</script>
<style scoped>
li.page-item {
  display: inherit;
}
button.page-link {
  display: inline-block;
}
button.page-link {
  font-size: 20px;
  color: #29b3ed;
  font-weight: 500;
}
.offset {
  width: 800px !important;
  margin: 20px auto;
}
</style> 



