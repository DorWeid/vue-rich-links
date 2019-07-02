<template>
  <li>
    <img :src="imageUrl" width="100px" height="100px" v-on:click="openUrl" />
    <article>
      <h3>{{title}}</h3>
      <p>{{description}}</p>
    </article>
    <button v-on:click="$emit('delete', url)">X</button>
  </li>
</template>

<script>
export default {
  name: "Link",
  props: {
    url: String,
    title: String,
    description: String,
    imageUrl: String
  },
  methods: {
    openUrl() {
      window.open(this.url, "_blank");
    }
  }
};
</script>

<style scoped>
li {
  display: flex;
  flex-direction: row;
  background-color: lightgrey;
  border-radius: 10px;
  height: 100px;
}

li img:hover {
  cursor: pointer;
}

li img {
  border-top-left-radius: 10px;
  border-bottom-left-radius: 10px;
}

li article {
  margin-left: 10px;
  width: 80%;
  text-align: left;
}

li article h3 {
  font-size: 14px;
  font-weight: 600;
}

li article p {
  max-height: 50%;
  font-size: 10px;
  width: 100%;
}
</style>