<template>
  <div class="container-fluid">
    <Header
      title="Vultus"
      ref="header"
      subTitle="Expression Analysis Tool"
      v-if="!isChartContainerVisible"
    />
    <svg xmlns="http://www.w3.org/2000/svg" style="display: none">
      <symbol id="check-circle-fill" fill="currentColor" viewBox="0 0 16 16">
        <path
          d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zm-3.97-3.03a.75.75 0 0 0-1.08.022L7.477 9.417 5.384 7.323a.75.75 0 0 0-1.06 1.06L6.97 11.03a.75.75 0 0 0 1.079-.02l3.992-4.99a.75.75 0 0 0-.01-1.05z"
        />
      </symbol>
      <symbol id="info-fill" fill="currentColor" viewBox="0 0 16 16">
        <path
          d="M8 16A8 8 0 1 0 8 0a8 8 0 0 0 0 16zm.93-9.412-1 4.705c-.07.34.029.533.304.533.194 0 .487-.07.686-.246l-.088.416c-.287.346-.92.598-1.465.598-.703 0-1.002-.422-.808-1.319l.738-3.468c.064-.293.006-.399-.287-.47l-.451-.081.082-.381 2.29-.287zM8 5.5a1 1 0 1 1 0-2 1 1 0 0 1 0 2z"
        />
      </symbol>
      <symbol
        id="exclamation-triangle-fill"
        fill="currentColor"
        viewBox="0 0 16 16"
      >
        <path
          d="M8.982 1.566a1.13 1.13 0 0 0-1.96 0L.165 13.233c-.457.778.091 1.767.98 1.767h13.713c.889 0 1.438-.99.98-1.767L8.982 1.566zM8 5c.535 0 .954.462.9.995l-.35 3.507a.552.552 0 0 1-1.1 0L7.1 5.995A.905.905 0 0 1 8 5zm.002 6a1 1 0 1 1 0 2 1 1 0 0 1 0-2z"
        />
      </symbol>
    </svg>
    <div class="container pt-5" v-if="!isChartContainerVisible">
      <div class="row g-5">
        <div class="col">
          <div class="p-3 mt-3 border rounded-pill bg-light shadow-lg">
            <VideoFrame @catchExpressionDetails="getExpressionDetails" />
          </div>
        </div>
        <div class="container col">
          <div
            class="alert alert-danger d-flex align-items-center justify-content-center"
            role="alert"
            v-if="isTimerInvalid"
            ref="invalidAlert"
          >
            <svg
              class="bi flex-shrink-0 me-2"
              width="24"
              height="24"
              role="img"
              aria-label="Danger:"
            >
              <use xlink:href="#exclamation-triangle-fill" />
            </svg>
            <div>Time should not exceed 60 minutes mark!</div>
          </div>
          <div
            class="alert alert-primary d-flex align-items-center justify-content-around"
            role="alert"
            v-if="isTimerOver"
            ref="timeOverAlert"
          >
            <svg
              class="bi flex-shrink-0 me-2"
              width="24"
              height="24"
              role="img"
              aria-label="Info:"
            >
              <use xlink:href="#info-fill" />
            </svg>
            <div>Time is up! Processing...</div>
            <div class="spinner-border" role="status"></div>
          </div>
          <div
            class="container shadow-lg mt-4 p-3 bg-light border border-3 rounded-pill"
          >
            <CountdownTimer
              @catchTimerValidity="updateTimerValidity"
              @catchTimerOver="onTimerOver"
            />
          </div>
          <!-- <div class="container" v-if="isChartContainerVisible">
            <div class="p-3 border bg-light rounded-pill mt-3">
              {{ expression }}
            </div>
          </div> -->
        </div>
      </div>
    </div>
    <ChartsDisplay :toPlotData="this.chartData" v-else />
  </div>
</template>

<script>
import Header from "@/components/Header.vue";
import VideoFrame from "@/components/VideoFrame.vue";
import CountdownTimer from "@/components/CountdownTimer.vue";
import ChartsDisplay from "@/components/ChartsDisplay.vue";

export default {
  name: "App",
  components: {
    Header,
    VideoFrame,
    CountdownTimer,
    ChartsDisplay,
  },
  metaInfo: {
    title: "Vultus",
  },
  data: () => {
    return {
      expression: "",
      isChartContainerVisible: false,
      isTimerInvalid: false,
      isTimerOver: false,
      chartData: {
        moodFreq: {},
        timeWiseMood: {},
        expressionEncodings: {},
        seriesData: [],
      },
      stopStreamFunc: null,
    };
  },
  methods: {
    getExpressionDetails(details) {
      this.expression = details.expression;
      this.stopStreamFunc = details.stopStream;
      if (!this.isTimerOver) {
        this.chartData.moodFreq = details.moodFreq;
        this.chartData.timeWiseMood = details.timeWiseExpression;
        this.chartData.expressionEncodings = details.expressionEncodings;
        this.chartData.seriesData = details.seriesData;
      }
    },

    updateTimerValidity(isTimerInvalid) {
      this.isTimerInvalid = isTimerInvalid;
    },

    onTimerOver(isTimerOver) {
      this.isTimerOver = isTimerOver;
      const sleep = (seconds) =>
        setTimeout(() => {
          if (this.isTimerOver) {
            this.isChartContainerVisible = true;
            this.stopStreamFunc();
          }
        }, seconds * 1000);
      sleep(2);
    },
  },
  created() {
    document.title = "Vultus";
  },
};
</script>

<style>
body {
  font-family: "Arvo", serif;
  background: linear-gradient(
      rgba(186, 100, 240, 0.15),
      rgba(168, 104, 208, 0.15)
    ),
    url("~@/assets/bg.jpeg");
  color: #7f9ad5;
}
.btn {
  box-shadow: 5px 5px 5px #deafd5;
  color: #a186be;
}

.btn-start-timer:hover {
  box-shadow: 5px 5px 5px #90d4c2;
  color: #90d4c2;
}
.btn-start-video:hover {
  box-shadow: 5px 5px 5px #81a8db;
  color: #81a8db;
}
.btn-stop-video:hover {
  box-shadow: 5px 5px 5px #f07d90;
  color: #f07d90;
}
.btn-stop-timer:hover {
  box-shadow: 5px 5px 5px #f07d90;
  color: #f07d90;
}
.btn-reset-timer:hover {
  box-shadow: 5px 5px 5px #ac7ed6;
  color: #ac7ed6;
}

.spinner {
  color: #ac7ed6;
}
</style>
