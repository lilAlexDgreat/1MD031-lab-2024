<template>
  <div id="header">
    <h1 id="headertext">Welcome to BurgerOnline</h1>
  </div>

  <section id="burgers">
    <h2>Select burger</h2>
    <p>This is where you execute burger selection</p>

    <div class="wrapper">
      <Burger
        v-for="burger in burgers"
        :burger="burger"
        :key="burger.name"
        @orderedBurger="addToOrder($event)"
      />
    </div>
  </section>

  <section id="info">
    <h2>Customer information</h2>
    <p>This is where you provide necessary information</p>

    <h3>Delivery information:</h3>

    <p>
      <label for="firstname">Full name</label><br>
      <input
        v-model="fname"
        type="text"
        id="name"
        name="fn"
        required="required"
        placeholder="First- and last name"
      >
    </p>

    <p>
      <label for="email">Email address</label><br>
      <input
        v-model="email"
        type="email"
        id="email"
        name="em"
        required="required"
        placeholder="E-mail address"
      >
    </p>

    <p>
      <label for="payment">Payment options</label><br>
      <select v-model="paytype" id="payment" name="pay">
        <option selected="selected">Credit card</option>
        <option value="cash">Cash?</option>
        <option value="paypall">Paypall</option>
        <option value="swish">Swish</option>
      </select>
    </p>

    <p>
      Gender <br>
      <label for="male">Male</label>
      <input v-model="gender" type="radio" id="male" name="gndr" value="male"> <br>

      <label for="female">Female</label>
      <input v-model="gender" type="radio" id="female" name="gndr" value="female"> <br>

      <label for="not">Do not wish to provide</label>
      <input v-model="gender" type="radio" id="not" name="gndr" value="not provided"> <br>
    </p>

    <button @click="addOrder" type="submit">
      <img src="../../sendbutton.png" width="100">
    </button>
  </section>

  
  <div id="mapdiv">
    <div id="map" @click="setLocation">
      <div id="dots">
        <div
        :style="{
          left: location.x + 'px',
          top: location.y + 'px',
          position: 'absolute'
        }"
        >
        T
      </div>
    </div>
  </div>
</div>
<hr>
{{orderedBurgers}}
</template>






<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, imgURL, kCal, spicy, lactose, contains, amount) {
  this.name = name;
  this.imgURL = imgURL;
  this.kCal = kCal;
  this.spicy = spicy;
  this.lactose = lactose;
  this.contains = contains;
  this.amountOrdered = amount;
}

const menu1 = [
  new MenuItem("The Classic Burger", "../../Classichamburger.jpg", 600, "1/10", "Lactose", "burger", 0),
  new MenuItem("The Chicken Burger", "../../Chickenburger.jpg", 500, "8/10", "Non lactose", "chicken", 0),
  new MenuItem("The Cheese Burger", "../../Cheeseburger.png", 15000, "-1/10", "Lactose", "cheese", 0)
];

export default {
  name: 'HomeView',
  components: { Burger },

  data() {
    return {
      burgers: menu,
      fname: "",
      email: "",
      paytype: "",
      gender: "",
      orderedBurgers: {},
      location: { x: 0, y: 0 }
    };
  },

  methods: {
    getOrderNumber() {
      return Math.floor(Math.random() * 100000);
    },
    addOrder(event) {
      var burglist = Object.keys(this.orderedBurgers);
      var orderlist = [];
      for (let i = 0; i < burglist.length; i++) {
        if (this.orderedBurgers[burglist[i]] > 0 ){
          orderlist.push(burglist[i] + ": " + this.orderedBurgers[burglist[i]] + "x");
        };
      }
      orderlist.push(
      " - " + this.fname + " (" + this.email + ", " + this.paytype + ", " + this.gender + ")"
      );

      console.log(orderlist)

      
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: this.location,
        orderItems: orderlist
      });
    },

    addToOrder(event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log("hej");
    },
        setLocation(event) {
      const offset = event.currentTarget.getBoundingClientRect();

      this.location = {
        x: event.clientX - offset.left - 10,
        y: event.clientY - offset.top - 10
      };
    },

    sendb() {
      console.log(this.fname, this.email, this.paytype, this.gender);
    }
  }
};
</script>

<style>
#mapdiv {
  overflow: scroll;
  margin-left: 8%;
  margin-right: 8%;
  margin-top: 2%;
  border-radius: 20px;
}

#map {
  background: url("/img/polacks.jpg");
  width: 1920px;
  height: 1078px;
}

body {
  font-size: 12pt;
  font-family: "courier new", "sans serif";
  background-color: darkgray;
}

.nonvegan {
  color: brown;
}
.lactos {
  color: grey;
}
.spicy {
  color: red;
}

#info {
  background-color: rgb(44, 44, 44);
  color: white;
  margin-left: 2%;
  margin-right: 2%;
  padding: 1%;
  border: 2px dashed white;
}

button:hover {
  background-color: black;
}
button {
  background-color: white;
  border-radius: 25%;
  cursor: grab;
}

.wrapper {
  display: grid;
  grid-gap: 50px;
  grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
}

#header {
  height: 200px;
  background-image: url(../../headerburger.jpg);
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(255, 255, 255, 0.3);
  background-blend-mode: lighten;
}

#headertext {
  color: black;
}
#burgers p,h2{
  margin-left: 4%;
}
</style>
