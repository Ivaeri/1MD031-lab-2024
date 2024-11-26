<template>
  <div class="Box a">
    <div class="chooseAmmount">
      <button class="decreaseButton" v-on:click="decreaseAmmount">-</button>
      <button class="addButton" v-on:click="addAmmount">+</button><br>
    </div>
    Antal: {{ ammountOrdered }}
      <h4> 
          {{burger.name}} 
      </h4>
      <img v-bind:src="burger.img" class="burgarbild">
      <ul>
          <li>
            {{ burger.description }}
          </li>
          <li>
              {{ burger.kCal }} kCal
          </li>
          <li v-if="burger.lactose "class="laktos">
              Innehåller <span>laktos</span>
          </li>
          <li v-else class="laktos">
              <span>laktosfri</span>
          </li>
          <li v-if="burger.gluten"class="gluten">
              Innehåller <span>gluten</span>
          </li>
          <li v-else class="gluten">
              <span>glutenfri</span>
          </li>
      </ul>
  </div>
</template>

<script> //exporterar nedan till Homeview
export default {
  
  name: 'OneBurger',
  props: {
    burger: Object
  },

  data: function() {
    return{
        ammountOrdered: 0,
      }
  },
  methods: {
    addAmmount(){
      this.ammountOrdered+=1
      this.$emit('orderedBurger', {name: this.burger.name, ammount: this.ammountOrdered})

    },
    decreaseAmmount(){
      if (this.ammountOrdered > 0){
        this.ammountOrdered-=1
        this.$emit('orderedBurger', {name: this.burger.name, ammount: this.ammountOrdered})
      }

    }
  }
}



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  nav ul {
      display: grid;
      grid-template-columns: repeat(auto-fill, 9.25em);
      gap: 1em;
      padding: 0;
  }
  .burgarbild{
    width: 230px;
    height: 200px;
  }

  nav li {
      display: block;
      background-color: grey;
      padding: 1em;
  }
  .laktos span{
    font-weight: bold;
  }
  .gluten span{
    font-weight: bold;
  }
  .chooseAmmount{
    display: inline;
    width: 250px;
  }
  .addButton{
    background-color: rgb(39, 194, 67);
    font-weight: bold;
    width: 30px;
    height: 20px;
  }
  .decreaseButton{
    background-color: rgb(195, 43, 32);
    font-weight: bold;
    width: 30px;
    height: 20px;
  }

</style>