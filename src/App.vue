<template>
  <div>
    <div class="container">
      <div class="round_num">Round: {{ round }}</div>
      <div class="game_field">
        <img
          :src="win"
          :class="[buttons[0].active ? 'active' : '']"
          @click="press(0, $event.target)"
        />

        <img
          :src="bullet"
          :class="[buttons[1].active ? 'active' : '']"
          @click="press(1, $event.target)"
        />

        <img
          :src="chubaka"
          :class="[buttons[2].active ? 'active' : '']"
          @click="press(2, $event.target)"
        />

        <img
          :src="iphone"
          :class="[buttons[3].active ? 'active' : '']"
          @click="press(3, $event.target)"
        />
      </div>
      <div :class="[lostbanner ? 'show' : 'hidden', 'lost']">
        You lost at round {{ lostround }}
      </div>
      <button :disabled="play" class="start" @click="start()">start</button>
      <div class="lvlchose">
        <div><input type="radio" v-model="lvl" value="1500" /> 1.5 second</div>
        <div><input type="radio" v-model="lvl" value="1000" /> 1 second</div>
        <div><input type="radio" v-model="lvl" value="400" /> 0.4 second</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      buttons: {
        0: { color: "red", active: false },
        1: { color: "blue", active: false },
        2: { color: "green", active: false },
        3: { color: "yellow", active: false },
      },
      run: false,
      round: 1,
      sequence: [],
      clicks: 1,
      lvl: 1500,
      play: false,
      lostround: 1,
      lostbanner: false,
      sounds: [require('@/assets/win.mp3'), require('@/assets/bullet.mp3'), require('@/assets/chuy.mp3'), require('@/assets/iphone.mp3')],
      win: require('@/assets/win.jpg'),
      bullet: require('@/assets/bullet.jpg'),
      chubaka: require('@/assets/chubaka.jpg'),
      iphone : require('@/assets/iphone.jpg'),
    };
  },
  methods: {
    async press(target, e) {
      if (!this.run) {
        if (this.clicks < this.round) {
          e.classList.add("active");

          new Audio(this.sounds[target]).play();

          await new Promise((r) => {
            setTimeout(() => r(), 200);
          });
          e.classList.remove("active");
          if (this.sequence[this.clicks] === target) {
            this.clicks += 1;
            if (this.clicks == this.round) {
              this.round += 1;
              this.start();
            }
          } else {
            this.lostround = this.round;
            this.lostbanner = true;
            this.round = 1;
            this.sequence = [];
            this.play = false;
          }
        }
      }
    },
    async start() {
      console.log(this.sounds)
      this.lostbanner = false;
      this.play = true;
      this.run = true;
      this.clicks = 0;
      await new Promise((r) => {
        setTimeout(() => r(), 300);
      });
      for (let i = 0; i < this.round; i++) {
        await new Promise((r) => {
          setTimeout(() => r(), this.lvl / 2);
        });

        let current = null;
        if (i === this.round - 1) {
          current = this.getRandomInt(4);
          this.sequence.push(current);
        } else current = this.sequence[i];

        new Audio(this.sounds[current]).play();

        this.buttons[current].active = true;
        await new Promise((r) => {
          setTimeout(() => r(), this.lvl / 2);
        });
        this.buttons[current].active = false;
      }
      this.run = false;
    },
    getRandomInt(max) {
      return Math.floor(Math.random() * Math.floor(max));
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.container {
  max-width: 1200px;
  margin: 0 auto;
}
.game_field {
  width: 400px;
  height: 400px;
  margin: 20px auto;
  display: flex;
  flex-wrap: wrap;
  img {
    width: 200px;
    height: 200px;
    opacity: 0.5;
    &.active { 
      opacity: 0.9;
    }
  }
}

.start {
  display: block;
  margin: 10px auto;
  width: 300px;
  height: 40px;
  border-radius: 10px;
  font-size: 30px;
  text-transform: uppercase;
}
.round_num {
  text-align: center;
  font-size: 35px;
}
.lvlchose div {
  text-align: center;
}
.lost {
  text-align: center;
  font-size: 20px;
  &.hidden {
    color: white;
  }
  &.show {
    color: black;
  }
}
</style>
