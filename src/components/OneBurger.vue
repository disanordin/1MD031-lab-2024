<template>
  <div class="burger-item">
    <h4>{{ burger.name }}</h4>
    <img :src="getImage(burger.name)" width="200" height="200"/>
    <div>
      <ul>
        <li>{{ burger.kCal}} kCal</li>
        <li>{{ burger.lactose ? 'Contains lactose': 'Lactose free'}}</li>
        <li>{{ burger.gluten ? 'Contains gluten' : 'Gluten free'}}</li>
        {{ amountOrdered }}
        <button @click="decreaseAmount">-</button>
        <button @click="increaseAmount">+</button>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

data: function () {
  return{
      amountOrdered: 0,
  }
},

methods: {
    getImage(burgerName) {
      switch (burgerName) {
        case 'Krabby Patty':
          return "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitRww8FlCpl27v10IFm74j1DRLTmmo-kkoY8Pck-Y_wJq9J2Dry2omp7LoVLYvOETjklWn5Ooset8SvPuYuzqouZ2qLqx4lmMTyeSs3yLA_a7zmZVbCoRTFlHGw8HnNA6_-b83nkSYdybEVtIMN6lZGtmMg1D8niprwi5LJ_VkAzTONKmCfQU8/w1200-h630-p-k-no-nu/krabby-patty-d3bpdiefjjgf5cxvvcnjomm45m.jpg";
        case 'Best burger in New York':
          return 'https://static1.cbrimages.com/wordpress/wp-content/uploads/2024/02/kgdgcfmjc1dldhxdjzfv1ompjqp2d3wxvs4s5pqbfkg-jpg.jpg';
        case 'Big Kahuna Burger':
          return 'https://hips.hearstapps.com/esquire/assets/17/01/1483576575-big-kahuna.jpg';
        default:
          return '';
      }
    },
    decreaseAmount() {
        if (this.amountOrdered > 0){
          this.amountOrdered--;
          this.$emit("orderedBurger", {name: this.burger.name, amount: this.amountOrdered})
        }
    },
    increaseAmount () {
      this.amountOrdered++;
      this.$emit("orderedBurger", {name: this.burger.name, amount: this.amountOrdered})
    },

  }
}

</script>

<style scoped>
.burger-item {
  text-align: left;
}

.burger-item img {
  width: 100%;
  max-width: 300px;
}

.Ingredients {
  color: white;
}

#gluten {
  font-weight: bold;
}

#lactose {
  font-weight: bold;
}

</style>