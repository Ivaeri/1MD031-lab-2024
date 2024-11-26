<template>
    <body>
    <header>
        <img src="/img/download.jpeg" class="fullbreddbild">
        <h1>
            Välkommen till Ivars Burgare!
        </h1>
    </header>
    <main>
        <section id="burgardel">
            <h3>    
                Välj Burgare
            </h3>
            <p>
            Välj vilken burgare du är sugen på
            </p>
            <div class="wrapper"> 
              <Burger v-for= "burger in burgers"
              v-bind:burger="burger"
              v-bind:key="burger.name"
              v-on:orderedBurger="addToOrder($event)"/>
            </div>
 
        </section>
        <section id="kunddel"> 
            <h3>
                Kundinformation
            </h3>
            <p>
                Skriv in dina uppgifter nedan
            </p>
            <h4>
                Leveransinformation:
            </h4>
            <p>
                <label for="Namn"> Fullständigt namn:</label><br>
                <input type="text" id="Namn" required="required" placeholder="Ditt namn" v-model="fullName">
            </p>
            <p>
                <label for="epost"> Mejladress:</label><br>
                <input type="email" id="epost" required="required" placeholder="Din e-post" v-model="ePost">
            </p>
            <p>Välj din leveransadress på kartan</p>
            <div id="map-container">
                <div  id="map" v-on:click="setLocation">
                    <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}" >
                        T
                    </div>  
                </div>
            </div>
            <p>
                <label for="betalinfo">Betalningsmetoder:</label><br>
                <select id="betalinfo" v-model="paymentChoice">
                    <option value="Bankkort"> Bankkort</option>
                    <option value="Swish"> Swish</option>
                    <option value="Paypal"> Paypal</option>
                    <option value="Bitcoin"> Bitcoin </option>
                </select>
            </p>
            <p id="paymentcolor"> <span> Valt betalmedel: </span>{{ paymentChoice }}</p>

            <label> Kön:</label><br>
            <label for="kvinna">
                <input type="radio" id="kvinna" v-model="kön" name="kön" value="Kvinna"> Kvinna
            </label><br>
            <label for="man">
                <input type="radio" id="man" v-model="kön" name="kön" value="Man"> Man
            </label><br>
            <label for="annat">
                <input type="radio" id="annat" v-model="kön" name="kön" value="Annat"> Annat
            </label><br>

            <label for="ejangivet">
                <input type="radio" id="ejangivet" v-model="kön" name="kön" value="Ej angivet"> Vill ej ange
            </label><br><br>

            <button type="submit"x v-on:click="placeOrder">
                <img src = "/img/gronbock.jpg" width="25" height="25">
                Beställ
            </button>
        </section>
    </main>


    <footer>
        <hr>
        Ivars Burgers AB
    </footer>
   </body>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
import {ref} from 'vue'


const socket = io("localhost:3000");

function MenuItems(name, img, kCal, lactose, gluten, description){
  name = this.name
  img = this.img
  kCal = this.kCal
  lactose = this.lactose
  gluten = this.gluten
  description = this.description
}



export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    const fullName = ref("")
    const ePost = ref("")
    const adress = ref("")
    const houseNumber = ref("")
    const kön = ref("")
    return {
      burgers: menu,
      fullName,
      ePost,
      adress,
      houseNumber,
      paymentChoice: 'Bankkort',
      kön: 'Kvinna',
      orderedBurgers: {Orginalet: 0, Plantburger: 0, Bukraset: 0},
      location: { 
            x: 0,
            y: 0
          },
      orderInfo: {}    
    }
 
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

    },
    placeOrder() {
        this.orderInfo.fullName= this.fullName
        this.orderInfo.email = this.ePost
        this.orderInfo.gender = this.kön
        this.orderInfo.payment = this.paymentChoice
        socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y},
                                orderItems: this.orderedBurgers,
                                orderInfo: this.orderInfo
                              }
                 );
    },
    addToOrder(event){
        this.orderedBurgers[event.name]= event.ammount
    },
    setLocation(event){
        var offset = {x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top};
        this.location= { x: event.clientX - 10 - offset.x,
            y: event.clientY - 10 - offset.y }
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@200..700&display=swap');

@media screen and (max-width: 800px) {
    h1 {
        font-size: 6vw;
    }
}
body {
    font-family: "Oswald";
    font-size: 12pt;
}

h1 {
    font-family: "Oswald";
    font-size: 36pt;
}

main, header, footer, nav ul {
    margin-left: 15px;
    margin-right: 15px;
    margin-top: 5px;
    margin-bottom: 5px;
}
main {
    /* max-width: 40 rem*/
    background-color: white;
}

header {
    width: calc(100%-15px);
    height: 200px;
    overflow: hidden;
    position: relative;
}

header h1 {
    /*width: 40rem;*/
    margin: 0 auto;
    text-align: center;
    position: absolute;
    font-weight: bold;
    padding-left: 265px;
    padding-top: 75px;
}

.fullbreddbild{
    width: 100%;
    height: auto;
    position: absolute;
    opacity: 0.6;
}
nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}
.laktos span{
    font-weight: bold;
}
.gluten span{
    font-weight: bold;
}

#burgardel{
    background-color: black;
    color: white;
    border: 2px dashed white;
    padding-left: 10px;

}
#burgardel p {
    color: white;
}

.wrapper{
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 250px 250px 250px;
    
}

#kunddel{
    background-color: white;
    color: black;
    border: 3px dashed black;
    padding-left: 10px;
}
#kunddel p {
    color: black;
}

button{
    margin-bottom: 15px;
}

button:hover {
    background-color: lightgreen;
    cursor: pointer;
}

#paymentcolor span{
        color: red;
}

#map-container{
    padding-left: 20px;
    width:700px;
    height: 400px;
    overflow: scroll;
    position: relative;
}
#map{
    background: url('/img/polacks.jpg');
    width:1920px;
    height: 1078px;
    position: relative;
    cursor: crosshair;
}
#map div{
    position: absolute;
    color: red;
    font-size: 14px;
    background-color: black;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
}
</style>