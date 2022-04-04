<template>
  <div class="container my-3">
    <div class="container" v-if="!showLoading">
      <div class="position-absolute top-0 start-0 mx-4 mt-4">
        <button
          class="btn btn-outline-secondary rounded border-2"
          @click="reloadPage"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="22"
            height="22"
            fill="currentColor"
            class="bi bi-arrow-return-left"
            viewBox="0 0 16 16"
          >
            <path
              fill-rule="evenodd"
              d="M14.5 1.5a.5.5 0 0 1 .5.5v4.8a2.5 2.5 0 0 1-2.5 2.5H2.707l3.347 3.346a.5.5 0 0 1-.708.708l-4.2-4.2a.5.5 0 0 1 0-.708l4-4a.5.5 0 1 1 .708.708L2.707 8.3H12.5A1.5 1.5 0 0 0 14 6.8V2a.5.5 0 0 1 .5-.5z"
            />
          </svg>
        </button>
      </div>
    </div>
    <Header
      title="Visual Report"
      subTitle="Here's how you felt"
    />
    <div
      class="container d-flex justify-content-center mt-5"
      v-if="showLoading"
    >
      <div
        class="spinner spinner-grow"
        style="width: 5rem; height: 5rem"
        role="status"
      ></div>
    </div>
    <div class="container text-center mt-3" v-if="showLoading">
      <span class="lead" style="color: #ac7ed6">Please Wait...</span>
    </div>
    <div class="row mt-3 gx-5 gy-4" v-if="!showLoading">
      <!-- Mood Frequency Plot -->
      <div class="col-12">
        <bar-chart
          :data="this.toPlotData.moodFreq"
          title="Expression Frequency"
          xtitle="Frequency"
          ytitle="Expression"
          download="moodFreqPlot"
          :colors="[
            'darkcyan',
            'darkorange',
            'steelblue',
            'tomato',
            'darkgoldenrod',
            'purple',
            'black',
          ]"
        />
      </div>
      <div class="col">
        <!-- Time Wise plot -->
        <area-chart
          :data="this.toPlotData.timeWiseMood"
          title="Time wise Expressions"
          download="timeWisePlot"
          xtitle="Time Frame"
          ytitle="Expression Encodings"
        />
      </div>
    </div>
    <div class="row mt-4 gy-4" v-if="!showLoading">
      <div class="col-12">
        <pie-chart
          :data="this.toPlotData.moodFreq"
          title="Expression Donut"
          download="moodFreqPlot"
          :donut="true"
        />
      </div>
      <div class="col">
        <line-chart
          :data="this.toPlotData.seriesData"
          title="Expression Series"
          download="seriesPlot"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Header from "@/components/Header.vue";

export default {
  name: "ChartsDisplay",
  components: {
    Header,
  },
  props: {
    toPlotData: Object,
  },
  data: () => {
    return {
      showLoading: true,
    };
  },
  methods: {
    reloadPage() {
      location.reload();
    },
  },
  mounted() {
    setTimeout(() => {
      this.showLoading = false;
    }, 3500);
  },
};
</script>
