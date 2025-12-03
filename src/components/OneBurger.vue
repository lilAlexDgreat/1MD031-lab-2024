<template>

              <div class="box">
                <h2>{{burger.name}}</h2>
                <img v-bind:src="burger.imgURL" width="300">
                <ul>
                    <li>Contains {{burger.contains}}</li>
                    <li>{{burger.spicy}} spicy</li>
                    <li>Approx. {{burger.kCal}}</li>
                    <li>{{burger.allergy}}</li>
                  </ul>
              <p>quantity ordered {{ amountOrdered }}</p>
                <button @click="add"><h3>&nbsp;+&nbsp;</h3></button>
                <button @click="remove"><h3>&nbsp;-&nbsp;</h3></button>
              </div>



</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

  data() {
    return {
      amountOrdered: 0
    };
  },

  methods: {
    add() {
      this.amountOrdered++;
      console.log("one " + this.burger.name + " added, current amount: " + this.amountOrdered);

      this.$emit('orderedBurger', { 
        name: this.burger.name,
        amount: this.amountOrdered
      });
    },

    remove() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
      }
      console.log("one " + this.burger.name + " removed, current amount: " + this.amountOrdered);
            this.$emit('orderedBurger', { 
        name: this.burger.name,
        amount: this.amountOrdered
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>


.box {
    background-color: orange;
    border-radius: 10px;
    padding-left: 10px;
    padding-right: 10px;
    justify-self: center;
    margin-bottom: 20px;
    padding-bottom: 10px;
}
.box button{
  margin-left: 23%;
  background-color: brown;
  color: white;
  height: 50px;
  width: 50px;
}
.a { }
.b { }
.c { }

</style>