<template>
  <div class="app">
    <h1>Welcome to the Links App</h1>
    <p>Simply add a link of your choice !</p>
    <input v-model="url" placeholder="..." />
    <button v-on:click="onAddLink">Add Link</button>
    <br />
    <span v-if="errorMessage">{{errorMessage}}</span>
    <hr />
    <ul>
      <Link
        v-on:delete="onRemoveLink"
        v-bind:key="link.imageUrl"
        v-bind="link"
        v-for="link in links"
      />
    </ul>
  </div>
</template>

<script>
import Link from "./Link";

// TODO: this should be kept in .env
const apiKey =
  "f545c696-d679-4385-8eac-19451db54a26" || "TEMP_NOT_TO_FINISH_LIMIT";

export default {
  name: "App",
  components: {
    Link
  },
  data() {
    return {
      url: "",
      links: [],
      errorMessage: ""
    };
  },
  created() {
    this.links = this.getLinks();
  },
  methods: {
    onRemoveLink: function(url) {
      const idxToRemove = this.links.findIndex(l => l.url === url);

      if (idxToRemove !== -1) {
        this.links.splice(idxToRemove, 1);
      }

      this.updateLinks();
    },
    async onAddLink() {
      try {
        if (this.links.find(l => l.url === this.url)) {
          throw new Error("link already exists");
        }

        // update state
        this.links.push(await this.getLinkMetadata(this.url));
        this.errorMessage = "";

        // update storage
        // note: because we are not actually using an operation that could fail, we assume it always succeeds, therefore update it after the state has been updated
        // (localStorage API doesnt supply much)
        this.updateLinks();
      } catch (e) {
        // todo: proper error handling here
        this.errorMessage = e.message;
      }
    },
    getLinks() {
      return JSON.parse(localStorage.getItem("linksMeta") || "[]");
    },
    updateLinks() {
      localStorage.setItem("linksMeta", JSON.stringify(this.links));
    },
    async getLinkMetadata(url) {
      const response = await fetch(
        `https://opengraph.io/api/1.1/site/${encodeURIComponent(
          url
        )}?app_id=${apiKey}`
      );
      const {
        openGraph: { image, title, description }
      } = await response.json();

      return { url, title, description, imageUrl: image.url };
    }
  }
};
</script>

<style scoped>
.app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
span {
  color: red;
}
h3 {
  margin: 40px 0 0;
}
ul {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  margin: 0;
  padding: 0;
}
ul li {
  margin: 10px;
  width: 80%;
}
a {
  color: #42b983;
}
</style>
