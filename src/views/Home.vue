<template>
  <div class="home">
    <link rel="stylesheet" href="https://bootswatch.com/4/superhero/bootstrap.min.css">
<v-container grid-list-xl>
  <v-layout row justify-start>
    <v-flex sm3>
      <h4>{{time}}</h4>
    </v-flex>
    <v-flex sm1>
      <h2 align="right" >{{topNum}}</h2>
    </v-flex>
    <v-flex sm4>
      <h4 align="right" >Right: {{right}}</h4>
    </v-flex>
    <v-flex sm4>
      <h4 align="right" >Wrong: {{wrong}}</h4>
    </v-flex>

  </v-layout>
  <v-layout row justify-start>
    <v-flex sm3>
      <h2 align="right" >
      +</h2>
    </v-flex>
    <v-flex sm1>
      <h2 align="right">{{botNum}}</h2>
    </v-flex>
    <v-flex justify-end sm4>

    </v-flex>
    <v-flex sm4>
      <h2></h2>
    </v-flex>
  </v-layout>
  <v-layout row>
    <v-flex  sm3>
    </v-flex>
    <v-flex sm1>
     <h2 align="right" >__</h2>
    </v-flex>
  </v-layout>
  <v-layout row>
    <v-flex  sm3>
      <h4>{{count}} of 25</h4>
    </v-flex>
    <v-flex sm2>
    <form @submit.prevent="checkValue" class="sm-3">
      <div class="form-group">
        <input v-model="answer" type="number" class="form-control"
            id="answer" placeholder="Equals" autofocus>
      </div>
      <button  type="submit"  class="btn btn-primary">Next!</button>
      <button :disabled="resetVal" @click="resetGame" type="button"
              class="btn btn-primary">Restart!</button>
       <button @click="pauseGame" type="button"
              class="btn btn-primary">Pause!</button>
    </form>
    </v-flex>
    <v-flex justify-end sm2>
    </v-flex>
   <v-flex justify-center sm2>
    <div class="list-unstyled" v-for="(ans, index) in previousRight" :key="index">
      <ul>
      <li class="media">
      <h4 >{{ans}}</h4>
      </li>
      </ul>
    </div>
    </v-flex>
    <v-flex sm1>
    </v-flex>
   <v-flex justify-center sm2>
    <div class="list-unstyled" v-for="(ans, index) in previousWrong" :key="index">
      <ul>
      <li class="media" >
      <h4>{{ans}}  <s>{{previousWrongAns[index]}}</s></h4>
      </li>
      </ul>
    </div>
    </v-flex>
  </v-layout>
<v-layout row justify-start>
    <v-flex sm3>
      <input v-model="topNumMax" type="number" class="form-control"
      id="topNumMax" placeholder="Largest Top">
      <input v-model="botNumMax" type="number" class="form-control"
      id="topNumMax" placeholder="Largest Bot">
    </v-flex>
</v-layout>
</v-container>
<v-snackbar
  v-model="snackbar"
  :bottom="y === 'bottom'"
  :color="color"
  :left="x === 'left'"
  :multi-line="mode === 'multi-line'"
  :right="x === 'right'"
  :timeout="timeout"
  :top="y === 'top'"
  :vertical="mode === 'vertical'"
>
  {{ text }}
</v-snackbar>
</div>
</template>
<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue';

export default {
  name: 'home',
  data: () => ({
    count: 1,
    answer: '',
    overlay: false,
    bigTop: 10,
    bigBot: 10,
    topNum: Math.floor((Math.random() * 10)),
    botNum: Math.floor((Math.random() * 10)),
    wrong: 0,
    right: 0,
    previousRight: [],
    previousWrong: [],
    previousWrongAns: [],
    resetVal: true,
    pauseVal: false,
    // Snackbar settings
    color: 'success',
    mode: '',
    snackbar: false,
    text: '',
    timeout: 1000,
    x: null,
    y: 'top',
    // Clock setting
    running: false,
    stoppedDuration: 0,
    timeBegan: null,
    timeStopped: null,
    time: '00:00:00.000',
    started: null,
  }),
  components: {

  },
  created() {
    this.start();
    this.bigTop = this.$route.query.bigTop;
    this.bigBot = this.$route.query.bigBot;
  },
  methods: {
    pauseGame() {
      this.pauseVal = !this.pauseVal;
      if (this.pauseVal === true) {
        this.stop();
        this.resetVal = false;
      } else {
        this.start();
        this.resetVal = true;
      }
    },
    retryWrong() {

    },
    resetGame() {
      this.count = 1;
      this.answer = '';
      this.overlay = false;
      this.topNum = Math.floor((Math.random() * this.bigTop));
      this.botNum = Math.floor((Math.random() * this.bigTop));
      this.wrong = 0;
      this.right = 0;
      this.previousRight = [];
      this.previousWrong = [];
      this.previousWrongAns = [];
      this.resetVal = true;
      this.pauseVal = false;
      this.reset();
      this.start();
    },
    checkValue() {
      const rightAns = this.topNum + this.botNum;
      if (rightAns === Number(this.answer)) {
        this.text = 'Correct!';
        this.color = 'success';
        this.snackbar = true;
        const stack = `${this.topNum.toString()} + ${this.botNum.toString()}  = ${this.answer.toString()}`;
        this.previousRight.push(stack);
        this.topNum = Math.floor((Math.random() * this.bigTop));
        this.botNum = Math.floor((Math.random() * this.bigBot));
        this.answer = '';
        this.right = this.right + 1;
        if (this.count < 25) {
          this.count += 1;
        } else {
          this.stop();
          this.resetVal = false;
        }
      } else {
        this.text = 'Wrong!';
        this.color = 'success';
        this.snackbar = true;
        const stack = `${this.topNum.toString()} + ${this.botNum.toString()}  = ${rightAns.toString()}`;
        this.previousWrong.push(stack);
        this.previousWrongAns.push(this.answer.toString());
        this.topNum = Math.floor((Math.random() * this.bigTop));
        this.botNum = Math.floor((Math.random() * this.bigBot));
        this.answer = '';
        this.wrong = this.wrong + 1;
        if (this.count < 25) {
          this.count += 1;
        } else {
          this.stop();
          this.resetVal = false;
        }
      }
      // console.log(rightAns);
      // console.log(this.answer);
    },
    reset() {
      this.running = false;
      clearInterval(this.started);
      this.stoppedDuration = 0;
      this.timeBegan = null;
      this.timeStopped = null;
      this.time = '00:00:00.000';
    },
    start() {
      if (this.running) return;
      if (this.timeBegan === null) {
        this.reset();
        this.timeBegan = new Date();
      }
      if (this.timeStopped !== null) {
        this.stoppedDuration += (new Date() - this.timeStopped);
      }
      this.started = setInterval(this.clockRunning, 10);
      this.running = true;
    },
    stop() {
      this.running = false;
      this.timeStopped = new Date();
      clearInterval(this.started);
    },
    clockRunning() {
      const currentTime = new Date();
      const timeElapsed = new Date(currentTime - this.timeBegan - this.stoppedDuration);
      const hour = timeElapsed.getUTCHours();
      const min = timeElapsed.getUTCMinutes();
      const sec = timeElapsed.getUTCSeconds();
      const ms = timeElapsed.getUTCMilliseconds();

      this.time = `${this.zeroPrefix(hour, 2)}:${this.zeroPrefix(min, 2)}:${this.zeroPrefix(sec, 2)}.${this.zeroPrefix(ms, 3)}`;
    },
    zeroPrefix(num, digit) {
      let zero = '';
      for (let i = 0; i < digit; i += 1) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    },
  },
};
</script>
<style>

.btn {
  min-width: 90px;
  padding: 15px 32px;
}

div.btn__content {
  padding-left: 16px;
}

div.card__title {
  padding-left: 0px;
}

div.card__actions {
  padding-left: 0px;
}
</style>
