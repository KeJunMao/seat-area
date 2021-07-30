<template>
  <div class="simulator">
    <SeatSelect
      :hall="hall"
      :seats="seats"
      :cols="cols"
      height="300px"
      @change="select"
    ></SeatSelect>
    扫码预览更多
    <img
      src="https://i.loli.net/2021/07/31/A45etf1sJ2ZMBHl.png"
      alt="爱票公众号"
    />
  </div>
</template>

<script>
import SeatSelect from "./components/SeatSelect.vue";
import data from "./components/data";
export default {
  components: {
    SeatSelect,
  },
  data() {
    return { ...data.data };
  },
  methods: {
    select(rowIndex, colIndex) {
      const seat = this.seats[rowIndex].columns[colIndex];
      const seatL = this.seats[rowIndex].columns[colIndex + 1];
      const seatR = this.seats[rowIndex].columns[colIndex - 1];
      const seats = {
        seat,
        seatL,
        seatR,
      };
      const status = seats.seat.s ? 0 : 1;
      seats.seat.s = status;
      if (seat.st === "R" || seat.st === "L") {
        seats[`seat${seat.st}`].s = status;
      }
    },
  },
};
</script>

<style>
#app {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}
.simulator {
  box-sizing: border-box;
  overflow: hidden;
  border-radius: 12px;
  box-shadow: #ebedf0 0 4px 12px;
  width: 100vw;
  height: 100vh;
}
@media (min-width: 415px) {
  .simulator {
    width: 360px;
    height: 618px;
  }
}
</style>
