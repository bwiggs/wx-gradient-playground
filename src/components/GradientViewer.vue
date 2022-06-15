<template>
  <div>
    <h1>Gradient</h1>
    <code>{{hoverValue}}</code>
    <div class="product" v-for="product in productsGen" :key="product.name">
      <h2>{{product.name}}</h2>
      <div class="gradient">
        <span
          class="range"
          v-for="g in product.stops"
          :key="g.stop"
          @mouseenter="hoverValue = g.stop"
          :style="style(g, product.stops)"
        ></span>
      </div>
    </div>
  </div>
</template>

<script>
import Rainbow from "/src/rainbow";

export default {
  name: "GradientViewer",
  props: {
  },
  data() {
    return {
      hoverValue: 0,
      products: [
        {
          name: "vel",
          precision: 1,
          stops: [
            { stop: -140, color: "PeachPuff" },
            { stop: -70, color: "red" },
            { stop: -10, color: "darkred" },
            { stop: 0, color: "#666666" },
            { stop: 10, color: "green" },
            { stop: 70, color: "lime" },
            { stop: 140, color: "PaleGreen" },
          ],
        },
        {
          name: "vel",
          precision: 1,
          stops: [
            { stop: -140, color: "#f91473" },
            { stop: -100, color: "#151f93" },
            { stop: -90, color: "#236fb3" },
            { stop: -50, color: "#57fa63" },
            { stop: -10, color: "#456742" },
            { stop: 0, color: "#634f50" },
            { stop: 10, color: "#6e2e39" },
            { stop: 50, color: "#F6508A" },
            { stop: 90, color: "#FA814B" },
            { stop: 100, color: "#dd603c" },
            { stop: 140, color: "#520106" },
          ],
        },
        {
          name: "sw",
          precision: 0.1,
          stops: [
            { stop: 0, color: "black" },
            { stop: 1, color: "#222222" },
            { stop: 9, color: "#999999" },
            { stop: 10, color: "burlywood" },
            { stop: 12, color: "gold" },
            { stop: 15, color: "orange" },
            { stop: 17, color: "red" },
            { stop: 27, color: "Fuchsia" },
            { stop: 35, color: "yellow" },
            { stop: 60, color: "lime" },
          ],
        },
        {
          name: "zdr",
          precision: 0.1,
          stops: [
            { stop: -5, color: "black" },
            { stop: 0, color: "#cccccc" },
            { stop: 2, color: "gold" },
            { stop: 5, color: "red" },
            { stop: 7, color: "hotpink" },
            { stop: 10, color: "pink" },
            { stop: 11, color: "purple" },
          ],
        },
        {
          name: "ref",
          precision: 0.1,
          stops: [
            { stop: 1, color: "black" },
            { stop: 10, color: "#666666" },
            { stop: 15, color: "rosybrown" },
            { stop: 20, color: "salmon" },
            { stop: 25, color: "salmon" },
            { stop: 30, color: "Indianred" },
            { stop: 35, color: "mediumspringgreen" },
            { stop: 40, color: "green" },
            { stop: 50, color: "gold" },
            { stop: 60, color: "red" },
            { stop: 65, color: "darkred" },
            { stop: 70, color: "blue", blend: false},
            { stop: 71, color: "cyan" },
            { stop: 75, color: "white" },
          ],
        },
      ],
    };
  },
  computed: {
    productsGen() {
      let rb = new Rainbow();
      return this.products.map((prod) => {

        let min = prod.stops[0].stop;
        let max = prod.stops[prod.stops.length - 1].stop;

        let steps = (max - min) / (prod.precision);

        let gradient = new Array(steps);

        let scale = ((min, max) => {
          return (v) => {
            return Math.floor(
              ((steps - 1) / (max - min)) * (v - max) + steps - 1
            );
          };
        })(min, max);


        prod.stops.forEach((curr, idx, a) => {
          let scaledIdx = scale(curr.stop);
          let next = a[idx + 1];

          if (next) {
            let scaledNextIdx = scale(next.stop);
            let fillIdxCount = scaledNextIdx - scaledIdx;

            rb.setNumberRange(0, fillIdxCount);
            rb.setSpectrum(curr.color, next.color);

            for (let index = 0; index <= fillIdxCount; index++) {
              let fillIdx = scaledIdx + index;
              let stop = curr.stop + index * prod.precision;
              let color = (curr.blend === false) ? rb.colourAt(fillIdxCount) : rb.colourAt(index);
              gradient[fillIdx] = { color, stop };
            }
          }
        });

        return {
          name: prod.name,
          stops: gradient
        }
      });
    },
  },
  methods: {
    style(g, gradient) {
      return {
        background: `#${g.color}`,
        width: `${100 / gradient.length}%`,
      };
    },
    output() {},
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.gradient {
  border: 1px solid #222;
  height: 30px;
}
span.range {
  height: 100%;
  display: inline-block;
  margin: 0;
  padding: 0;
}
</style>
