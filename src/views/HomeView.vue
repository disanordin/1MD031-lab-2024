<template>
    <header class="header">
            <img class="header_img" src="https://images.ctfassets.net/nwbqij9m1jag/5oLuW3CHDy0E2sMsw0ugGc/30bf03bad7dc8115e9fc995877054b8a/table-seating-kol-helsingborg-press-image-michel-cavalli.jpg" 
            alt="Restaurang">
            <div>
                <h1 class="headline">Welcome to BurgerOnline</h1>
            </div>
            
        </header>

    <main>
        <section id = "burger" class="burger-grid">
          <Burger v-for="burger in burgers"
           v-bind:burger="burger"
           v-bind:key="burger.name"
           v-on:orderedBurger="addToOrder($event)"/>
        </section>

        <section id="information">
            <h2>Costumer information</h2>
            <h3>Fill in the information boxes</h3>

            <form>
            <p>
                <label for="name">Full name</label><br>
                <input type="text" id="Fullname" name="fn" v-model="name" required placeholder="First- and Last name">
            </p>

            <p>
                <label for="E-mail">E-mail</label><br>
                <input type="email" id="Email" name="mail" v-model="mail" required placeholder="E-mail address">
            </p>

            <p>
                <label for="Payment Method">Payment Method</label><br>
                <select id="Payment" name="payment" v-model="paymentMethod">
                <option>Swish</option>
                <option>Credit Card</option>
                <option>Master Card</option>
                <option>Klarna</option>
                </select>
            </p>

            <p>
                <label>Gender:</label><br>

                <input type="radio" id="male" name="gender" value="male" v-model="gender">
                <label for="male">Male</label><br>

                <input type="radio" id="female" name="gender" value="female" v-model="gender">
                <label for="female">Female</label><br>

                <input type="radio" id="other" name="gender" value="other" v-model="gender" checked="checked">
                <label for="other">Other</label>
            </p>
            </form>
        </section>  

        <section>
            <button type="submit" @click="addOrder">
                    <img src="https://cdn.pixabay.com/photo/2013/07/12/17/01/shopping-cart-151684_1280.png" width=17px
                    >Place my Order!
            </button>
        </section>

        
      
        <div style="width: 800px; height: 500px; overflow: scroll; border: 2px solid black;">
            <div id="map" v-on:click="setLocation">
                <div 
                    v-if="location.x !== 0 && location.y !== 0" 
                    :style="{ 
                        position: 'absolute', 
                        left: location.x + 'px', 
                        top: location.y + 'px', 
                        color: 'red', 
                        fontSize: '20px', 
                        fontWeight: 'bold' 
                    }"
                >
                    T
                </div>

            click here
            </div>
        </div>


        <footer>
        </footer>  
    </main>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function menuItem(name, image, kCal, gluten, lactose){
this.name = name;
this.image =image;
this.kCal = kCal;
this.gluten = gluten;
this.lactose = lactose;
}

/*const burgers = [
new menuItem("Krabby Patty", "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitRww8FlCpl27v10IFm74j1DRLTmmo-kkoY8Pck-Y_wJq9J2Dry2omp7LoVLYvOETjklWn5Ooset8SvPuYuzqouZ2qLqx4lmMTyeSs3yLA_a7zmZVbCoRTFlHGw8HnNA6_-b83nkSYdybEVtIMN6lZGtmMg1D8niprwi5LJ_VkAzTONKmCfQU8/w1200-h630-p-k-no-nu/krabby-patty-d3bpdiefjjgf5cxvvcnjomm45m.jpg", 500, true, true),
new menuItem("Best burger in New York","https://static1.cbrimages.com/wordpress/wp-content/uploads/2024/02/kgdgcfmjc1dldhxdjzfv1ompjqp2d3wxvs4s5pqbfkg-jpg.jpg", 400, true, false),
new menuItem("Big Kahuna Burger","https://hips.hearstapps.com/esquire/assets/17/01/1483576575-big-kahuna.jpg", 300, true, false)
];

*/

export default {
name: 'HomeView',
components: {
Burger
},
data: function () {
return {
    burgers: menu,
    name: "",
    mail: "",
    paymentMethod: "Swish",
    gender: "Other",
    orderedBurgers: {},
    location: {x:0, y:0}

}
},
methods: {
setLocation(event) {
    const rect = event.target.getBoundingClientRect();
    this.location.x = event.clientX - rect.left;
    this.location.y = event.clientY - rect.top;
},


getOrderNumber: function () {
  return Math.floor(Math.random()*100000);
},
addBurger: function () {
  this.amountOrdered += 1;
  this.$emit('orderedBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
  );
},
addToOrder: function (event) {
  if (event.amount === 0) {
    delete this.orderedBurgers[event.name]; 
  } else {
    this.orderedBurgers[event.name] = event.amount; 
  }
},
addOrder: function () {
    if (!this.name || !this.mail || !this.paymentMethod || !this.gender) {
        alert("Please fill in all required fields before placing your order.");
        return;
    }
  let order = {
    name: this.name,
    mail: this.mail,
    paymentMethod: this.paymentMethod,
    gender: this.gender,
    orderId: this.getOrderNumber(),
    details: {
      x: this.location.x,
      y: this.location.y,
    },
    orderItems: this.orderedBurgers
  };
  socket.emit("addOrder", order);
},
}}
</script>

<style>

div[style*="overflow: scroll"] {
margin-left: 20px;
margin-bottom: 90px;
}

#map {
background: url("/img/polacks.jpg") no-repeat center center;
background-size: contain;
width: 1920px;
height: 1078px; 
position: relative;
}
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
font-family: 'Times New Roman', Times, serif;  
}

section {
margin: 20px;
}

button {
margin-bottom: 20px;
}

#burger {
background-color: black;
color: white;
border: 2px dotted white;
/*padding: 0px 10px 0px*/
}

#burger div{
padding: 10px;

}

#information {
background-color:  rgb(240, 179, 179);
color: #803d3d;
border: 2px dotted #803d3d;
padding: 0px 10px 0px
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

button:hover {
background-color: #3498db;
cursor: pointer;
}


.header {
padding: 0px 20px;
position: relative;
height: 300px;
overflow:hidden

}

.header_img {
width: 100%;
height: auto;
opacity: 0.6;
}

.headline {
position: absolute;
top: 50%;
left: 50%;
transform: translate(-50%, -50%);
color: white;
font-size: 2.5em; 
}


.burger-grid {
display: grid;
grid-template-columns: repeat(3, 1fr);
grid-gap: 20px;
}

.burger-item {
text-align: left;
}

.burger-item img {
width: 100%;
max-width: 300px;
}

@media screen and (max-width: 800px) {
h1 {
    font-size: 6vw;
}
}


</style>