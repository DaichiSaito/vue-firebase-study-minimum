<template>
  <ul>
    <li v-for="smoothie in smoothies" :key="smoothie.id">
      <h3>{{smoothie.title}}</h3>
      <router-link :to="{ name: 'editSmoothie', params: {id: smoothie.id}}">
        <i>編集</i>
      </router-link>
      <i @click="deleteSmoothie(smoothie.id)">削除</i>
      <ul class="ingredients">
        <li v-for="(ing, index) in smoothie.ingredients" :key="index">{{ing}}</li>
      </ul>
    </li>
  </ul>
</template>

<script>
import db from "@/firebase.js";
export default {
  name: "index",
  data() {
    return {
      smoothies: []
    };
  },
  methods: {
    deleteSmoothie(id) {
      // delete doc from firestore
      db.collection("smoothies")
        .doc(id)
        .delete()
        .then(() => {
          this.smoothies = this.smoothies.filter(smoothie => {
            return smoothie.id != id;
          });
        })
        .catch(err => {
          console.log(err);
        });
    }
  },
  created() {
    // fetch data from firestore
    db.collection("smoothies")
      .get()
      .then(snapshot => {
        snapshot.forEach(doc => {
          let smoothie = doc.data();
          smoothie.id = doc.id;
          this.smoothies.push(smoothie);
        });
      });
  }
};
</script>