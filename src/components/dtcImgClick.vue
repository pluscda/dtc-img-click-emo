<template>
  <div class="dtc-img-click" ref="dtcImgClick">
    <div ref="dtcImg" class="dtc-img" :style="{backgroundImage:'url('+ dataUrl +')'}"></div>
  </div>
</template>

<script>
export default {
  name: "dtcClickImgCmp",
  data() {
    return {
        dataUrl :'',
    };
  },
  components: {},
  computed: {},
  methods: {
    loadImgUrl() {
        let img = new Image();
        img.setAttribute("crossOrigin",'anonymous');
        img.src = this.url + '?' + new Date().getTime();
        img.onload = () => {
            let canvas = document.createElement("canvas");
            let width = img.width;
            let height = img.height;
            canvas.width = width;
            canvas.height = height;
            canvas.getContext('2d').drawImage(img, 0,0, width, height);
            this.dataUrl = canvas.toDataURL('image/jpeg');
            // construct clickable points
            this.constructPoints();
        }

    },

    constructPoints() {
      if (!this.points) {
        return;
      }
      let eventList = [];
      //step 1: get img el
      const img = this.$refs.dtcImg;
      //step 2:  create new canvas element that will have same size of passed in img
      let el = document.createElement("canvas");
      el.width = this.width;
      el.height = this.height;

      //step 3: append into dom right below the img
      img.append(el);
      //step 4: create 2d context(s) with msg inside
      this.points.forEach(({ x: _x, y: _y, msg }) => {
        let ctx = el.getContext("2d");
        let x = _x;
        let y = _y;
        let w = 18;
        // Create circle
        let circle = new Path2D();
        ctx.fillStyle = "rgb(200,0,0)";
        circle.arc(x, y, w, 0, 2 * Math.PI, false);
        ctx.fill(circle);

        // draw the msg into circle
        let ctx2 = el.getContext("2d");
        ctx2.font = "16pt Calibri";
        ctx2.fillStyle = "white";
        ctx2.textAlign = "center";
        ctx2.fillText(msg, x, y + 7);
        // push to event list for listening the click event
        eventList.push({ ctx, circle });
      }); // end of step 4
      //step 5 listen for click event for each 2d ctx
      el.onclick = event => {
        for (let i = 0; i < eventList.length; ++i) {
          const e = eventList[i];
          if (e.ctx.isPointInPath(e.circle, event.offsetX, event.offsetY)) {
            const msg = this.points[i].msg;
            this.$emit("dtc-img-click", msg);
            break;
          }
        }
      };
       const elRect = el.getBoundingClientRect();
       const mainEl = this.$refs.dtcImgClick ; // hold for toggle the cursor
       //step 6 show cursor when needed
        el.onmousemove = event => {
        let cursor = false;
        for (let i = 0; i < eventList.length; ++i) {
           const e = eventList[i];
           const myX = event.clientX - elRect.left;
           const myY = event.clientY - elRect.top;
          if (e.ctx.isPointInPath(e.circle, myX, myY)) {
            mainEl.style.cursor = "pointer";
            cursor = true;
            break;
          }
        }
        if (!cursor) {
            mainEl.style.cursor = "default";
        }
      };
      
    }
  },
  props: ["width", "height", "url", "points"],
  mounted() {
    // set width / height based on pass in props
    const el = this.$refs.dtcImgClick;
    el.style.setProperty("--width", this.width + "px");
    el.style.setProperty("--height", this.height + "px");
    this.loadImgUrl();
   
  },
  beforeDestroy() {},
  watch: {}
};
</script>


<style lang="scss" scoped>
.dtc-img-click {
  --width: 0px;
  --height: 0px;
  width: var(--width) !important;
  height: var(--height) !important;
  .dtc-img {
    width: var(--width) !important;
    height: var(--height) !important;
    background: transparent no-repeat top left / cover;
    position: relative;
    > canvas {
      position: absolute;
      min-width: var(--width) !important;
      min-height: var(--height) !important;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
    }
  }
}
</style>

