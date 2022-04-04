<template>
  <div class="container pt-3 mx-auto rounded-pill border border-3 bg-white">
    <div class="container mx-2">
      <div class="container mx-auto">
        <div class="d-flex align-items-center justify-content-around">
          <input
            :readonly="isReadOnly"
            type="text"
            class="form-control w-25"
            v-model="timerDetails.minutes"
          /><span class="text-muted">Min</span>
          <input
            :readonly="isReadOnly"
            type="text"
            class="form-control w-25"
            v-model="timerDetails.seconds"
          /><span class="text-muted">Sec</span>
        </div>
      </div>
      <div class="row w-50 mx-5 mt-4 gy-3 mb-3 mr-3">
        <button
          class="btn rounded-pill border-2 btn-start-timer"
          v-if="!timerDetails.isTimerRunning"
          @click="startTimer"
        >
          Start
        </button>
        <button
          class="btn rounded-pill btn-stop-timer"
          v-else
          @click="stopTimer"
        >
          Stop
        </button>
        <button class="btn rounded-pill btn-reset-timer" @click="resetTimer">
          Reset
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Timer",
  data: () => {
    return {
      timerDetails: {
        minutes: 0,
        seconds: 0,
        isTimerRunning: false,
        isTimerInvalid: false,
        isTimeUp: false,
        timer: null,
      },
      isReadOnly: false, // 'timer' holds the setInterval method so that it can be cleared by referring to this 'timer' data property
    };
  },

  methods: {
    startTimer() {
      if (
        this.timerDetails.seconds < 0 ||
        this.timerDetails.seconds > 3600 ||
        this.timerDetails.minutes < 0 ||
        this.timerDetails.minutes > 60
      ) {
        this.timerDetails.isTimerInvalid = true;
        this.$emit("catchTimerValidity", this.timerDetails.isTimerInvalid);
      } else {
        this.timerDetails.isTimerRunning = true;
        this.isReadOnly = true;

        this.timerDetails.timer = setInterval(() => {
          if (this.timerDetails.seconds == 0 && this.timerDetails.minutes > 0) {
            this.timerDetails.seconds = 60;
            this.timerDetails.minutes--;
          }
          if (
            this.timerDetails.minutes == 0 &&
            this.timerDetails.seconds == 0
          ) {
            this.timerDetails.isTimeUp = true;
            this.$emit("catchTimerOver", this.timerDetails.isTimeUp);
            this.resetTimer();
          }
          if (this.timerDetails.seconds > 0) {
            // Condition to prevent extra decrement of seconds
            this.timerDetails.seconds--;
          }
        }, 1000);
      }
    },

    stopTimer() {
      this.timerDetails.isTimerRunning = false;
      this.isReadOnly = false;
      clearInterval(this.timerDetails.timer);
    },

    resetTimer() {
      [this.timerDetails.minutes, this.timerDetails.seconds] = [0, 0];
      this.timerDetails.isTimerRunning = false;
      this.isReadOnly = false;
      clearInterval(this.timerDetails.timer);
    },
  },
};
</script>
