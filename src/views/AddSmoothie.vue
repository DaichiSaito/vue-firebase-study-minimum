<template>
  <div>
    <h2>Add New Smoothie Recipe</h2>
    <form @submit.prevent="addSmoothie">
      <div>
        <label for="title">タイトル:</label>
        <input type="text" name="title" v-model="title">
      </div>
      <div v-for="(ing, index) in ingredients" :key="index">
        <label for="ingredient">材料:</label>
        <input type="text" name="ingredient" v-model="ingredients[index]">
        <i @click="deleteIng(index)">削除</i>
      </div>
      <div>
        <label for="add-ingredient">材料を追加 (タブで追加):</label>
        <input type="text" name="add-ingredient" @keydown.tab.prevent="addIng" v-model="another">
      </div>
      <div>
        <p v-if="feedback">{{ feedback }}</p>
        <button>Add Smoothie</button>
      </div>
    </form>
  </div>
</template>

<script>
import db from "@/firebase.js";
export default {
  name: "addSmoothie",
  data() {
    return {
      title: null,
      ingredients: [],
      another: null,
      feedback: null
    };
  },
  methods: {
    addSmoothie() {
      if (this.title) {
        this.feedback = null;
        db.collection("smoothies")
          .add({
            title: this.title,
            ingredients: this.ingredients
          })
          .then(() => {
            this.$router.push({ name: "index" });
          })
          .catch(err => {
            console.log(err);
          });
      } else {
        this.feedback = "You must enter a smoothie title";
      }
    },
    addIng() {
      if (this.another) {
        this.ingredients.push(this.another);
        this.another = null;
        this.feedback = null;
      } else {
        this.feedback = "You must enter a value to add another ingredient";
      }
    },
    deleteIng(_index) {
      this.ingredients = this.ingredients.filter((ingredient, index) => {
        return index != _index;
      });
    }
  }
};
</script>

