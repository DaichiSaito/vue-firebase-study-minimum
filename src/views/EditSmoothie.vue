<template>
  <div v-if="smoothie">
    <h2>Edit {{ smoothie.title }} Smoothie</h2>
    <form @submit.prevent="editSmoothie">
      <div>
        <label for="title">Smoothie title:</label>
        <input type="text" name="title" v-model="smoothie.title">
      </div>
      <div v-for="(ing, index) in smoothie.ingredients" :key="index">
        <label for="ingredient">Ingredient:</label>
        <input type="text" name="ingredient" v-model="smoothie.ingredients[index]">
        <i @click="deleteIng(index)">削除</i>
      </div>
      <div>
        <label for="add-ingredient">Add an ingredient (press tab to add):</label>
        <input type="text" name="add-ingredient" @keydown.tab.prevent="addIng" v-model="another">
      </div>
      <div>
        <p v-if="feedback">{{ feedback }}</p>
        <button>Update Smoothie</button>
      </div>
    </form>
  </div>
</template>

<script>
import db from "@/firebase.js";

export default {
  name: "editSmoothie",
  data() {
    return {
      smoothie: null,
      another: null,
      feedback: null
    };
  },
  methods: {
    editSmoothie() {
      if (this.smoothie.title) {
        this.feedback = null;

        db.collection("smoothies")
          .doc(this.smoothie.id)
          .update({
            title: this.smoothie.title,
            ingredients: this.smoothie.ingredients
          })
          .then(() => {
            this.$router.push({ name: "Index" });
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
        this.smoothie.ingredients.push(this.another);
        this.another = null;
        this.feedback = null;
      } else {
        this.feedback = "You must enter a value to add another ingredient";
      }
    },
    deleteIng(_index) {
      this.smoothie.ingredients = this.smoothie.ingredients.filter(
        (ingredient, index) => {
          return index != _index;
        }
      );
    }
  },
  created() {
    db.collection("smoothies")
      .doc(this.$route.params.id)
      .get()
      .then(doc => {
        this.smoothie = doc.data();
        this.smoothie.id = doc.id;
      });
  }
};
</script>
