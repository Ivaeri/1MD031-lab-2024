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
                <p v-if="isActiveName":class="{ active1: isActiveName }">Vänligen fyll i ett namn</p>
            </p>
            <p>
                <label for="epost"> Mejladress:</label><br>
                <input type="email" id="epost" required="required" placeholder="Din e-post" v-model="ePost">
                <p v-if="isActiveEmail" :class="{ active2: isActiveEmail }">Vänligen fyll i en email-adress</p>
            </p>
            <p>Välj din leveransadress på kartan
                <p v-if="isActiveMap" :class="{ active3: isActiveMap }"> Vänligen välj en leveransadress på kartan</p>
            </p>
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
        <div v-if="orderPlaced" :class="{ active3: orderPlaced }">
            <h4 class="recieptheader"> Orderbekräftelse</h4>
            <div>
                <h5> Produkter:</h5>
                <ul>
                    <li v-for="(antal, name) in orderedBurgers"
                    :key="name">
                        <p v-if="antal !== 0">
                            {{ name }}: {{ antal }}
                        </p>
                    </li>
                </ul>
            </div>
            <div>
                <h5> Kundinformation:</h5>
                <ul>
                    <li v-for="info in orderInfo">
                        {{ info }}
                    </li>
                </ul>
            </div>
            
        </div>
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
      orderInfo: {},  
      isActiveName: false, 
      isActiveEmail: false,
      isActiveMap: false,
      orderPlaced: false,
    }
    },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    placeOrder() {
        this.resetWarningMessages();
        this.orderInfo.fullName= this.fullName
        this.orderInfo.email = this.ePost
        this.orderInfo.gender = this.kön
        this.orderInfo.payment = this.paymentChoice
        if (this.orderInfo.fullName == "") this.warningMessageName()
        else if (this.orderInfo.email == ""){this.warningMessageEmail()}
        else if (this.location.x == 0 && this.location.y == 0) {this.warningMessageMap()}
        else {
            socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y},
                                orderItems: this.orderedBurgers,
                                orderInfo: this.orderInfo
                              }
                 );
            this.showReceipt();
    }},
    addToOrder(event){
        this.orderedBurgers[event.name]= event.ammount
    },
    setLocation(event){
        var offset = {x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top};
        this.location= { x: event.clientX - 10 - offset.x,
            y: event.clientY - 10 - offset.y }
    },
    warningMessageName(){
        this.isActiveName = true;

    },
    warningMessageEmail(){
        this.isActiveEmail = true;
    },
    warningMessageMap(){
        this.isActiveMap = true;
        
    },
    resetWarningMessages(){
        this.isActiveName = false;
        this.isActiveEmail = false;
        this.isActiveMap = false;
    },
    showReceipt(){
        this.orderPlaced = true;
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
    background-color: white;
}

header {
    width: calc(100%-15px);
    height: 200px;
    overflow: hidden;
    position: relative;
}

header h1 {
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

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}

#burgardel{
    background-color: black;
    color: white;
    border: 2px dashed white;
    padding-left: 10px;

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
p.active1{
    color: red;
    display: inline;
}
p.active2{
    color:red;
    display:inline;
}
p.active3{
    color:red;

}

.active3{
    display: grid;
    grid-template-rows: auto 1fr;
    grid-template-columns: 1fr 1fr;
    gap: 15px;
    height: 25%;
    width: 25%;
    margin-top: 15px;
    background-color:rgb(98, 212, 142);

}
.recieptheader{
    grid-column: 1/-1;
    text-align: center;
}
.active3 div {
    margin-left: 10px;

}
</style>