<template>
  <div class="seat-select" :class="{ hideSeats }">
    <Hall
      :text="hall"
      :styles="hallStyle"
      :class-name="{ animate: animate }"
      @click="toggle"
    ></Hall>

    <SeatArea
      v-if="seats.length"
      :height="height"
      @transform="onTransform"
      @springback="onSpringback"
      @hammer="animate = false"
    >
      <template #screen-outside>
        <div
          class="seat-index"
          :style="seatIndexsStyle"
          :class="{ animate: animate }"
        >
          <div v-for="i in seatIndexs" :key="i" class="seat-index-item">
            {{ i }}
          </div>
        </div>
      </template>
      <div
        v-for="(row, rindex) in seats"
        :key="`${row.row_id}-${rindex}`"
        class="seat-row"
      >
        <div
          v-for="(col, index) in row.columns"
          :key="`${row.row_id}-${rindex}-${col.column_id}-${index}`"
          class="seat"
          :class="[
            col.st,
            activeClass(col),
            index === middlePosition ? 'seat-middle' : '',
          ]"
          @click="select(col, rindex, index)"
        ></div>
      </div>
      <div class="logo">
        <img src="/iTicket.png" alt="爱票科技logo" />爱票科技提供
      </div>
    </SeatArea>
  </div>
</template>

<script>
import SeatArea from "./SeatArea.vue";
import Hall from "./Hall.vue";

export default {
  components: {
    SeatArea,
    Hall,
  },
  props: {
    hall: {
      type: String,
      default: "",
    },
    seats: {
      type: Array,
      default: () => [],
    },
    cols: {
      type: Number,
      default: 0,
    },
    height: {
      type: String,
      default: "90vw",
    },
    private: {
      type: Boolean,
    },
    readonly: {
      type: Boolean,
    },
  },
  data() {
    return {
      hideSeats: this.readonly
        ? localStorage.getItem("chen-flod-seat") ?? false
        : false,
      x: 0,
      dx: 0,
      y: 0,
      scale: 0,
      animate: false,
    };
  },
  computed: {
    middlePosition() {
      return Math.round(this.cols / 2);
    },
    seatIndexs() {
      return this.seats.map((v) => v.row_num);
    },
    seatIndexsStyle() {
      return `transform: translate(0px, ${
        this.y + (1 - this.scale) * 30
      }px) scale(${this.scale})`;
    },
    hallStyle() {
      return `transform: translate(calc(-50% + ${this.x}px - ${this.dx}px), 0px)`;
    },
  },
  methods: {
    onSpringback() {
      this.animate = true;
    },
    onTransform({ x, y, scale, dx }) {
      this.x = x;
      this.y = y;
      this.scale = scale;
      this.dx = dx;
    },
    select(item, rindex, cindex) {
      if (item.st === "LK" || item.st === "E") return;
      this.$emit("change", rindex, cindex);
    },
    activeClass(col) {
      return { active: !this.private && col.s };
    },
    toggle() {
      if (this.readonly) {
        this.hideSeats = !this.hideSeats;
      }
    },
  },
};
</script>

<style lang="scss">
.seat-select {
  background: #f9f6f7;
  overflow: hidden;
  position: relative;

  .seat-index {
    background: rgba(0, 0, 0, 0.3);
    border-radius: 50px;
    color: #fff;
    display: flex;
    flex-direction: column;
    font-weight: bold;
    position: absolute;
    text-align: center;
    z-index: 1;

    &-item {
      box-sizing: border-box;
      font-size: 20px;
      height: 30px;
      margin: 3px;
      padding: 0 3px;
    }
  }

  .seats-loading {
    align-items: center;
    display: flex;
    justify-content: center;
  }

  .logo {
    align-items: center;
    color: #333;
    display: flex;
    font-size: 14px;
    justify-content: center;
    margin: 0 auto;
    margin-top: 10px;
    opacity: 0.5;

    img {
      height: 25px;
      margin-right: 5px;
    }
  }

  .seat-row {
    display: flex;
  }

  .seat {
    background: #fefefe;
    border: 1px solid #ccc;
    border-radius: 10px;
    box-sizing: border-box;
    height: 30px;
    margin: 3px;
    position: relative;
    width: 30px;

    &-middle::before {
      border-left: 0.02667rem dashed #aaa;
      box-sizing: border-box;
      content: "";
      display: block;
      height: 125%;
      margin-left: -4px;
      position: absolute;
    }

    &.active {
      background: darkseagreen;
      border: 1px solid darkseagreen;

      &::after {
        align-items: center;
        color: #fff;
        content: "✓";
        display: flex;
        font-size: 12px;
        font-weight: bold;
        height: 100%;
        justify-content: center;
        text-align: center;
      }
    }

    &.LK {
      background-color: red;
      border: 1px solid red;

      &::after {
        background-image: url("/iTicket.png");
        background-position: center;
        background-repeat: no-repeat;
        background-size: 90%;
        content: "";
        display: block;
        filter: invert(100%);
        height: 100%;
      }
    }

    &.E {
      background: transparent;
      border-color: transparent;
    }

    &.L {
      border-radius: 10px 10px 0;
      border-right: 0;
      margin-right: 0;
      width: 33px;
    }

    &.R {
      border-left: 0;
      border-radius: 10px 10px 10px 0;
      margin-left: 0;
      width: 33px;

      &.seat-middle::before {
        margin-left: 0;
      }
    }
  }
}
</style>
