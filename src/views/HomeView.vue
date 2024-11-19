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
              <Burger v-for= "burger of burgers"
              v-bind:burger="burger"
              v-bind:key="burger.name"/>
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
                <input type="text" id="Namn" required="required" placeholder="Ditt namn" name="fn">
            </p>
            <p>
                <label for="epost"> Mejladress:</label><br>
                <input type="email" id="epost" required="required" placeholder="Din e-post" name="fn">
            </p>
            <p>
                <label for="adress"> Gatunamn:</label><br>
                <input type="text" id="adress" required="required" placeholder="Adress" name="fn">
            </p>
            <p>
                <label for="Husnummer"> Husnummer:</label><br>
                <input type="number" id="Husnummer" required="required" placeholder="Ditt Husnummer" name="fn">
            </p>

            <p>
                <label for="betalinfo">Betalningsmetoder:</label><br>
                <select id="betalinfo" name="rcp">
                    <option selected="selected"> Bankkort</option>
                    <option> Swish</option>
                    <option> Paypal</option>
                    <option> Bitcoin </option>
                </select>
            </p>

            <label for="kön"> Kön:</label><br>
            <label>
                <input type="radio" name="kön" value="Kvinna" checked="checked"> Kvinna
            </label><br>
            <label>
                <input type="radio" name="kön" value="Man"> Man
            </label><br>

            <label>
                <input type="radio" name="kön" value="Annat"> Annat
            </label><br>

            <label>
                <input type="radio" name="kön" value="ejangivet"> Vill ej ange
            </label><br><br>

            <button type="submit" margin-bottom="15px">
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

const socket = io("localhost:3000");

function MenuItems(name, img, kCal, lactose, gluten){
  name = this.name
  img = this.img
  kCal = this.kCal
  lactose = this.lactose
  gluten = this.gluten

}
const burgerarr = [
                    { name: "Orginalet", img: "/img/Burgare1.jpg", kCal: 350, lactose: true, gluten: true },
                    { name: "Plant-Burger", img: "/img/Buragre2.jpg", kCal: 300, lactose: false, gluten: false },
                    { name: "Bukraset", img: "/img/Långburgare.jpg", kCal: 1000, lactose: true, gluten: true }
                    ];

console.log(burgerarr)
export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgerarr
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
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
    border: 1px dashed white;
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

.a{
    grid-column: 1;
}

.b{
    grid-column: 2;
}

.c{
    grid-column: 3;
}

#kunddel{
    background-color: white;
    color: black;
    border: 1px dashed black;
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

</style>