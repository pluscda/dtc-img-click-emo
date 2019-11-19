<template>
  <div class="dtc-demo">
    <div class="dtc-img-dummy"></div>
    <DtcImgClick
      @dtc-img-click="handleImgClick"
      :width="map.width"
      :height="map.height"
      :url="map.url"
      :points="map.points"
    ></DtcImgClick>
  </div>
</template>

<script>
import { mapGetters, mapActions, mapMutations } from "vuex";
import DtcImgClick from "../components/dtcImgClick";
export default {
  data() {
    return {
      clickedNum: 0,
      map: {
        width: 400,
        height: 400,
        url: "//unsplash.it/400/400",
        points: [
          { x: 40, y: 64, msg: "1b" },
          { x: 140, y: 64, msg: "2" }
        ]
      }
    };
  },
  components: { DtcImgClick },
  computed: {
    // ...mapGetters(["viewportBreakpoint", "searchBy"])
  },
  methods: {
    handleImgClick(msg) {
      alert("parent " + msg);
    }
  },

  mounted() {},
  beforeDestroy() {},
  watch: {},
  name: "dtcDemo"
};
</script>

<style lang="scss" scoped>
.dtc-demo {
  min-height: calc(100vh - 300px);
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-column-gap: 30px;
  justify-content: center;
  align-items: center;
  padding: 0 30px;
  .dtc-img-dummy {
    background: url(//unsplash.it/400/400) no-repeat top left / cover;
    width: 400px;
    height: 400px;
    position: relative;
    > canvas {
      cursor: pointer !important;
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
    }
  }

  .dct-result {
    border: 1px solid black;
    width: 400px;
    height: 400px;
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    .dct-msg {
      width: 100px;
      height: 30px;
      color: white;
      background: black;
      font-size: 16px;
      border-radius: 7px;
      text-align: center;
      line-height: 30px;
    }
  }
}
</style>
