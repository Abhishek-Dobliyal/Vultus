<template>
  <div
    class="card align-items-center d-flex justify-content-center rounded-pill border-3"
  >
    <div class="embed-responsive embed-responsive-4by3">
      <img
        :src="require(`../assets/${noVideoImg}`)"
        class="img-fluid py-4"
        alt="Responsive image"
        v-if="!isVideoStreaming"
      />
      <video
        width="250"
        height="250"
        ref="video"
        class="embed-responsive-item border rounded-pill mt-3 shadow-lg"
        muted
        autoplay
        v-else
      ></video>
    </div>
    <div class="card-body p-3">
      <p class="card-text" v-if="!isVideoStreaming">
        Make sure your face is visible.
      </p>
      <button
        @click="startVideo"
        class="btn rounded-pill btn-start-video"
        :disabled="this.isVideoStreaming"
      >
        Start Video
      </button>
      <button @click="stopVideo" class="btn rounded-pill mx-3 btn-stop-video">
        Stop Video
      </button>
    </div>
  </div>
</template>

<script>
import * as faceapi from "face-api.js";

export default {
  name: "VideoFrame",
  data: () => {
    return {
      isVideoStreaming: false,
      noVideoImg: "video.png",
      modelPath: "/models",
      toSend: {
        expression: "",
        timeWiseExpression: {},
        stopStream: null,
        seriesData: [],
        moodFreq: {
          neutral: 0,
          happy: 0,
          sad: 0,
          surprised: 0,
          angry: 0,
          disgusted: 0,
          fearful: 0,
        },
        expressionEncodings: {
          neutral: 1,
          sad: 2,
          happy: 3,
          surprised: 4,
          angry: 5,
          disgusted: 6,
          fearful: 7,
        },
      },
    };
  },

  async mounted() {
    try {
      await faceapi.loadTinyFaceDetectorModel(this.modelPath);
      await faceapi.loadFaceExpressionModel(this.modelPath);
    } catch (err) {
      console.log("Error:", err);
    }
  },

  methods: {
    startVideo() {
      this.isVideoStreaming = true;

      navigator.getUserMedia(
        { video: {} },
        (stream) => (this.$refs.video.srcObject = stream),
        (err) => console.error(err)
      );
    },

    stopVideo() {
      this.isVideoStreaming = false;
      let stream = this.$refs.video.srcObject;
      let tracks = stream.getTracks();

      tracks.forEach((track) => {
        track.stop();
      });

      this.$refs.video.srcObject = null;
    },

    initializeDetection() {
      this.$refs.video.addEventListener("play", () => {
        setInterval(async () => {
          await faceapi
            .detectAllFaces(
              this.$refs.video,
              new faceapi.TinyFaceDetectorOptions()
            )
            .withFaceExpressions()
            .then((detections) => {
              const expressionsJson = detections[0].expressions;

              let maxConfidence = 0;
              for (const key in expressionsJson) {
                if (expressionsJson[key] > maxConfidence) {
                  maxConfidence = expressionsJson[key];
                  this.toSend.expression = key;
                  this.toSend.moodFreq[key]++;

                  // Get time in the form HH::MM:SS
                  let dateObj = new Date();
                  let hour = dateObj.getHours();
                  let minutes = dateObj.getMinutes();
                  let seconds = dateObj.getSeconds();
                  let time = hour + ":" + minutes + ":" + seconds;

                  // Get expression series data ready
                  let seriesLength = this.toSend.seriesData.length;
                  let isKeyFound = false;
                  for (let i = 0; i < seriesLength; i++) {
                    if (this.toSend.seriesData[i].name == key) {
                      isKeyFound = !isKeyFound;
                      let dataSize = Object.keys(
                        this.toSend.seriesData[i].data
                      ).length;
                      this.toSend.seriesData[i].data[time] = dataSize + 1;
                      break;
                    }
                  }

                  if (!isKeyFound) {
                    let series = { name: "", data: {} };
                    series.name = key;
                    series.data[time] = 1;
                    this.toSend.seriesData.push(series);
                  }

                  this.toSend.timeWiseExpression[time] =
                    this.toSend.expressionEncodings[key];
                }
              }
              this.toSend.stopStream = this.stopVideo;
              this.$emit("catchExpressionDetails", this.toSend);
            })
            .catch((err) => {
              console.log(err);
            });
        }, 500);
      });
    },
  },

  updated() {
    if (this.$refs.video && this.isVideoStreaming) {
      this.initializeDetection();
    } else {
      console.log("Error in Updated!");
    }
  },
};
</script>
