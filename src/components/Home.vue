<template>
  <div class="layout col-20">
    <link
      rel="stylesheet"
      href="https://use.fontawesome.com/releases/v5.2.0/css/all.css"
      integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ"
      crossorigin="anonymous"
    >
    <div class="form-group row">
      <div class="col-11 input-group">
        <input
          class="col-4 my-0 py-1"
          v-model="search"
          type="text"
          placeholder="Search"
          aria-label="Search"
        >
        <div class="input-group-append">
          <span class="input-group-text" id="basic-text1">
            <i class="fas fa-search text-grey" aria-hidden="true"></i>
          </span>
        </div>
      </div>
    </div>

    <div class="form-group row">
      <div class="col-2">
        <input
          class="form-control"
          v-model="startDate"
          type="date"
          value="2011-08-19"
          id="example-date-input"
        >
      </div>
      <div class="col-2 end-date">
        <input class="form-control" v-model="endDate" type="date" value="10/7/2027" id="endDate">
      </div>
      <span class="filter">
        <i class="fas fa-filter" aria-hidden="true" @click="filterDays()"></i>
      </span>
    </div>
    <div class="continer overflow-auto col-4">
      <div class="card" v-for="post in filteredPosts " :key="post.id">
        <div class="card-body">
          <h5 class="card-title">{{post.name }}</h5>
          <h6 class="text-muted">{{post.location.name }} {{post._id}}</h6>
          <p class="card-text">{{post.location.address}}</p>
          <span>{{ display(post.dropWindows[0].startTime) }}</span>
          <span class="small">{{displayHour(post.dropWindows[0].endTime)}}</span>
          <span>{{ display(post.dropWindows[1].startTime) }}</span>
          <span class="small">{{displayHour(post.dropWindows[1].endTime )}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Content from "../assets/generated.json";
export default {
  name: "Home",
  data() {
    return {
      posts: [Content],
      search: "",
      startDate: "",
      startDatePost: "",
      endDate: "",
      endDatePost: "",
      serchFilterName: "",
      locationFilter: ""
    };
  },

  methods: {
    display(time) {
      var a = new Date(time).toLocaleDateString("en-US");
      return a;
    },
    displayHour(hour) {
      var s = new Date(hour).toLocaleTimeString("en-US");
      return s;
    }
  },
  computed: {
    filteredPosts() {
      return this.posts.filter(post => {
        this.endDatePost =
          post.dropWindows[1].startTime <= new Date(this.endDate).getTime();
        this.startDatePost =
          post.dropWindows[0].startTime >= new Date(this.startDate).getTime();
        this.serchFilterName = post.name
          .toLowerCase()
          .includes(this.search.toLowerCase());
        this.locationFilter = (
          post.location.name.toLowerCase() +
          " " +
          post._id
        ).includes(this.search.toLowerCase());

        if ((this.endDate == "" || this.startDate == "") && this.search == "")
          return post;
        else if (
          (this.serchFilterName || this.locationFilter) &&
          (this.endDatePost && this.startDatePost)
        ) {
          return post;
        } else if (
          this.endDatePost &&
          this.startDatePost &&
          this.search == ""
        ) {
          return post;
        } else if (
          (this.serchFilterName || this.locationFilter) &&
          this.endDate == ""
        ) {
          return post;
        }
      });
    }
  },
  created() {
    this.posts = Content;
  },
  mounted() {
    console.log(this.posts[0].name);
    // console.log(new Date(posts[0].dropWindows[1].startTime).toLocaleTimeString);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.fa-filter {
  padding-top: 10px;
}
.filter {
  border: 1px solid gainsboro;
  background-color: #ededed;
  padding-right: 10px;
  padding-left: 10px;
  margin-left: -15px;
}
.layout {
  margin: 20px;
}
.end-date {
  margin-left: -12px;
}
.continer {
  height: 40em;
  overflow: scroll;
  padding: 5px;
  background-color: rgba(214, 228, 243, 0.918);
  border: 1px solid grey;
}
.card {
  margin-bottom: 5px;
}
</style>