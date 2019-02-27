<template>
  <div id="app">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.2/css/all.css" integrity="sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr" crossorigin="anonymous">
    <div class='stars'>
      <div class='twinkling'>
        <div class='clouds'>
          <div v-if="warning" class="warning-card">
            <h1 style="color:white;margin-top:5%">Cannot move further {{trueDirection}}</h1>
            <button class="btn" @click="closeWarning" style="margin-left:95%;">X</button>
          </div>

          <div v-if="setup" class="card">
            <div v-if="warningMessage && setup">
              <div class="setupwarning">{{setupWarning}}</div>
            </div>

            <div v-if="gridSize">
              <h1>Grid Size</h1>
              <p>Max X-axis:</p>
              <input v-model="xMax" />
              <br>
              <p>Max Y-axis:</p>
              <input v-model="yMax" />
              <br>
              <button class="button-submit" @click="gridSize = false" style="margin-top:10%">Next</button>
            </div>

            <div v-if="gridSize == false">
              <p class="back" style="color:gray;margin-left: -90%;font-size: 15px;" @click="gridSize=true">⬅ Back</p>
              <p>Enter X-Axis Position:</p>
              <input v-model="x" />
              <br>
              <p>Enter Y-Axis Position:</p>
              <input v-model="y" />
              <br>
              <select placeholder="Select Direction" class="form-control" v-model="key">
                <option value="" disabled selected>Select Direction</option>
                <option class="custom-select" v-for="option in list_directions" :key="option.name" @click="setIndex(option.index)">{{option.name}}</option>
                <br>
              </select>
              <button class="button-submit" @click="completeSetup" style="margin-top:10%;width:150px;">Submit</button>
            </div>
          </div>

          <h1 style="font-family:impact" v-if="controllers">X:{{x}} : Y:{{y}} {{trueDirection}}</h1>

          <div v-if="controllers == true" style="padding-left:5%" class='controllers'>
            <input v-model="roverDirections" placeholder="Enter Rover Directions" style="width:500px;height:50px;position: relative;" />
            <button @click="directRover" style="width:15%;margin-left:2%;position: relative;" class="btn button-X">Submit</button>
            <button @click="newRover" style="width:15%;margin-left:2%;position: relative;" class="btn button-B">New Rover</button>
          </div>

          <i v-if="controllers" :style="{'margin-top': height,
            '-ms-transform': direction2,
            '-moz-transform': direction2,
            '-webkit-transform': direction2,
            'transform': direction2,
            'margin-left': width}" class="fas fa-space-shuttle" :class="direction" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  components: {},
  data() {
    return {
      submitButton: false,
      roverDirections: "", // get string input for directions
      setup: true,
      warning: false,
      warningMessage: false,
      setupWarning: "",
      gridSize: true,
      controllers: false,
      list_directions: [
        { name: "North", index: 3 },
        { name: "South", index: 1 },
        { name: "East", index: 0 },
        { name: "West", index: 2 }
      ],
      x: null,
      y: null,
      xMax: null,
      yMax: null,
      move: 0,
      mt: 15,
      degrees: [0, 90, 180, 270],
      index: 0,
      ml: 45,
      right: true,
      left: false,
      up: false,
      down: false,
      key: ""
    };
  },

  methods: {
    newRover() {
      this.roverDirections = "";
      this.setup = true;
      this.gridSize = false;
      this.controllers = false;
    },
    directRover() {
      var commands = this.roverDirections.split("");
      let vm = this;
      for (let i = 0; i < commands.length; i++) {
        if (commands[i].toLowerCase() == "l") {
          setTimeout(() => {
            vm.rotateLeft();
          }, 1000);
        } else if (commands[i].toLowerCase() == "r") {
          setTimeout(() => {
            vm.rotateRight();
          }, 1000);
        } else if (commands[i].toLowerCase() == "m") {
          setTimeout(() => {
            vm.moveRover();
          }, 1000);
        }
      }
    },
    setIndex(i) {
      this.index = i;
    },
    completeSetup() {
      this.controllers = true;
      this.setup = false;
    },
    closeWarning() {
      this.warning = false;
      this.controllers = true;
    },
    moveRover() {
      if (this.index == 0) {
        this.ml = parseInt(this.ml) + 4;
        this.x = parseInt(this.x) + 1;
      } else if (this.index == 1) {
        this.mt = parseInt(this.mt) + 4;
        this.y = parseInt(this.y) - 1;
      } else if (this.index == 2) {
        this.ml = parseInt(this.ml) - 4;
        this.x = parseInt(this.x) - 1;
      } else if (this.index == 3) {
        this.mt = parseInt(this.mt) - 4;
        this.y = parseInt(this.y) + 1;
      }
    },
    rotateRight() {
      if (this.index != 3) {
        this.index += 1;
      } else {
        this.index = 0;
      }
    },
    rotateLeft() {
      if (this.index != 0) {
        this.index -= 1;
      } else {
        this.index = 3;
      }
    },
    rotateUp() {
      this.right = false;
      this.left = false;
      this.up = true;
      this.down = false;
    },
    rotateDown() {
      this.right = false;
      this.left = false;
      this.up = false;
      this.down = true;
    }
  },
  watch: {
    x() {
      if (parseInt(this.x) > parseInt(this.xMax)) {
        if (this.setup == true) {
          this.setupWarning = "Must be smaller than the grid size";
          this.warningMessage = true;
        } else {
          this.controllers = false;
          this.warning = true;
          this.x = parseInt(this.x) - 1;
        }
      }
      if (parseInt(this.x) < parseInt(this.xMax)) {
        this.warningMessage = false;
      }
      if (parseInt(this.x) < 0) {
        this.controllers = false;
        this.warning = true;
        this.x = parseInt(this.x) + 1;
      }
    },
    y() {
      if (parseInt(this.y) > parseInt(this.yMax)) {
        if (this.setup == true) {
          this.setupWarning = "Must be smaller than the grid size";
          this.warningMessage = true;
        } else {
          this.controllers = false;
          this.warning = true;
          this.y = parseInt(this.y) - 1;
        }
      }
      if (parseInt(this.y) < parseInt(this.yMax)) {
        this.warningMessage = false;
      }
      if (parseInt(this.y) < 0) {
        this.controllers = false;
        this.warning = true;
        this.y = parseInt(this.y) + 1;
      }
    }
  },
  computed: {
    trueDirection() {
      if (this.index == 0) {
        return "East";
      } else if (this.index == 1) {
        return "South";
      } else if (this.index == 2) {
        return "West";
      } else {
        return "North";
      }
    },
    height() {
      return this.mt + "%";
    },
    width() {
      return this.ml + "%";
    },
    direction() {
      if (this.left == true) {
        return "rotate-left";
      } else if (this.right == true) {
        return "rotate-right";
      } else if (this.up == true) {
        return "rotate-up";
      } else if (this.down == true) {
        return "rotate-down";
      } else {
        return "";
      }
    },
    direction2() {
      return "rotate(" + this.degrees[this.index] + "deg)";
    }
  }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css?family=Work+Sans:400,700,900");
@import url("https://fonts.googleapis.com/icon?family=Material+Icons");
// @import "susy";
// @import "compass/reset";
$green: #2ecc71;
$red: #e74c3c;
$blue: #3498db;
$yellow: #f1c40f;
$purple: #8e44ad;
$turquoise: #1abc9c;
body {
  background: black;
  padding: 20px;
}

p {
  text-align: center;
  color: gray;
  font-family: Arial;
  font-size: 20px;
}
.setupwarning {
  height: 25px;
  width: 90%;
  background-color: $yellow;
  border-radius: 10px;
  text-align: center;
}

i {
  font-size: 50px;
  color: white;
  animation-delay: 2s;
  -webkit-transition: width 2s;
  transition: all ease-in-out 1.5s;
}

#app {
  background: black;
  border-radius: 4px;
  padding: 20px;
  transition: all 0.2s;
}

.back {
  cursor: pointer;
  color: gray;
}

li {
  margin: 8px 0;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

del {
  color: rgba(0, 0, 0, 0.3);
}

.buttons {
  vertical-align: top;
  max-width: 30em;
  margin: 0 auto;
}

h1 {
  text-align: center;
  font-size: 2.8em;
  font-weight: 800;
  white-space: pre;
  line-height: 0.85;
  color: #999;
}

hr {
  margin: 0 0 2em;
  border: 0;
}

button {
  font-family: "Work Sans", sans-serif;
  display: inline-block;
  border: 0;
  background: #bab9b9;
  color: white;
  font-weight: 600;
  border-radius: 2em;
  width: 10%;
  height: 2em;
  font-size: 2em;
  outline: none;
  border-bottom: 0.15em solid rgba(0, 0, 0, 0.5);
}

.button-submit {
  font-family: "Work Sans", sans-serif;
  display: inline-block;
  border: 0;
  background: #bab9b9;
  color: white;
  font-weight: 600;
  border-radius: 2em;
  width: 100%;
  height: 2em;
  font-size: 2em;
  outline: none;
  border-bottom: 0.15em solid rgba(0, 0, 0, 0.5);
}

button.active,
button:active {
  border-bottom: 0.15em solid transparent;
  transform: scale(0.9);
}

.button-circle {
  color: #fff;
}

.button-bar {
  width: 7em;
  font-size: 1.3em;
}

.button-axes,
.button-bar-mini {
  background: #222;
  color: white;
}

.button-axes {
  width: 1.6em;
  height: 1.6em;
  line-height: 1.3;
  border-radius: 0.2em;
  color: #666;
}

.button-bar-mini {
  width: 6em;
  font-size: 0.75em;
  background: #222;
  color: white;
  font-weight: 300;
  border-bottom: 0.3em solid rgba(0, 0, 0, 0.5);
}

.button-bar-mini.active,
.button-bar-mini:active {
  border-bottom: 0.3em solid transparent;
}

.button-A {
  background: #ed1d29;
}

.button-B {
  background: #939393;
}

.button-X {
  background: #025ac9;
}

.button-Y {
  background: #00a97a;
}

button .arrow {
  display: inline-block;
  width: 0;
  height: 0;
  vertical-align: middle;
  font-size: 0.35em;
  line-height: 0;
  border-color: rgba(0, 0, 0, 0.5);
  border: 0 solid transparent;
  color: white;
}

button .arrow-left {
  border-right-color: #999;
  border-right-width: 1em;
  border-bottom-width: 1em;
  border-top-width: 1em;
  margin-left: -0.25em;
}

button .arrow-right {
  border-left-color: #999;
  border-left-width: 1em;
  border-bottom-width: 1em;
  border-top-width: 1em;
  margin-right: -0.25em;
}

button .arrow-up {
  border-bottom-color: #999;
  border-bottom-width: 1em;
  border-left-width: 1em;
  border-right-width: 1em;
  margin-top: -0.25em;
}

button .arrow-down {
  border-top-color: #999;
  border-top-width: 1em;
  border-left-width: 1em;
  border-right-width: 1em;
  margin-bottom: -0.25em;
}

.rotate-down {
  -ms-transform: rotate(90deg);
  -moz-transform: rotate(90deg);
  -webkit-transform: rotate(90deg);
  transform: rotate(90deg);
}

.rotate-right {
}

.rotate-left {
  -ms-transform: rotate(180deg);
  -moz-transform: rotate(180deg);
  -webkit-transform: rotate(180deg);
  transform: rotate(180deg);
}

.rotate-up {
  -ms-transform: rotate(270deg);
  -moz-transform: rotate(270deg);
  -webkit-transform: rotate(270deg);
  transform: rotate(270deg);
}

.stars,
.twinkling,
.clouds {
  position: absolute;
  display: block;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  width: 100%;
  height: 100%;
}

.stars {
  z-index: 0;
  background: #000 url("https://image.ibb.co/mjnygo/stars.png") repeat top
    center;
}

.twinkling {
  z-index: 1;
  background: transparent url("https://image.ibb.co/ir1DE8/twinkling.png")
    repeat top center;
  animation: move-twink-back 200s linear infinite;
}

.clouds {
  z-index: 2;
  background: transparent url("https://image.ibb.co/bT4N7T/clouds.png") repeat
    top center;
  animation: move-clouds-back 200s linear infinite;
}

@keyframes move-twink-back {
  from {
    background-position: 0 0;
  }
  to {
    background-position: -10000px 5000px;
  }
}

@keyframes move-clouds-back {
  from {
    background-position: 0 0;
  }
  to {
    background-position: 10000px 0;
  }
}

.card {
  margin-left: 35%;
  margin-top: 15%;
  width: 300px;
  height: 450px;
  background: #f1f1f1;
  border-radius: 12px;
  padding: 35px;
  color: #f1f1f1;
  position: relative;
  background-size: contain;
}

.warning-card {
  margin-left: 25%;
  margin-top: 15%;
  width: 650px;
  height: 140px;
  background: $yellow;
  border-radius: 12px;
  padding: 35px;
  color: #f1f1f1 !important;
  position: relative;
  background-size: contain;
}

input {
  width: 100%;
  margin-top: 2%;
  height: 40px;
  border-radius: 10px;
  border-color: grey;
  border: none;
  text-align: center;
  font-size: 20px;
}

select {
  margin-top: 10%;
  height: 45px;
  font-size: 18px;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 0;
  cursor: pointer;
  display: inline-block;
  padding: 10px;
  width: 100%;
  color: #666;
}

.custom-select {
  margin-top: 10%;
  height: 20px;
  border: 1px solid #ccc;
  border-radius: 0;
  cursor: pointer;
  display: inline-block;
  padding: 10px;
  width: 100%;
  color: #666;
}
</style>
