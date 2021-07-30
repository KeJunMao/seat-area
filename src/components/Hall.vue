<template>
  <div class="hall-warpper" @click="$emit('click')">
    <div class="hall" :style="styles" :class="className">
      <svg
        width="212"
        height="31"
        viewBox="0 0 212 31"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <g filter="url(#filter0_d)">
          <path
            d="M11 15.5C11 15.5 57.5601 7 105.855 7C154.151 7 201 15.5 201 15.5"
            stroke="#D44973"
            stroke-width="2"
            stroke-linecap="round"
          />
        </g>
        <defs>
          <filter
            id="filter0_d"
            x="-0.000244141"
            y="0"
            width="212"
            height="30.5002"
            filterUnits="userSpaceOnUse"
            color-interpolation-filters="sRGB"
          >
            <feFlood flood-opacity="0" result="BackgroundImageFix" />
            <feColorMatrix
              in="SourceAlpha"
              type="matrix"
              values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0"
              result="hardAlpha"
            />
            <feOffset dy="4" />
            <feGaussianBlur stdDeviation="5" />
            <feComposite in2="hardAlpha" operator="out" />
            <feColorMatrix
              type="matrix"
              values="0 0 0 0 0.831373 0 0 0 0 0.286275 0 0 0 0 0.45098 0 0 0 0.5 0"
            />
            <feBlend
              mode="normal"
              in2="BackgroundImageFix"
              result="effect1_dropShadow"
            />
            <feBlend
              mode="normal"
              in="SourceGraphic"
              in2="effect1_dropShadow"
              result="shape"
            />
          </filter>
        </defs>
      </svg>
      <div class="hall-text">{{ text }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    text: {
      type: String,
      default: "",
    },
    styles: {
      type: String,
      default: "",
    },
    className: {
      type: Object,
      default: () => ({}),
    },
  },
};
</script>

<style lang="scss">
.hall {
  align-items: center;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  height: 30px;
  justify-content: center;
  left: 50%;
  position: relative;
  top: 0;
  z-index: 2;

  &-text {
    color: #c9c9c9;
    font-size: 12px;
    margin-top: -18px;
    text-align: center;
    white-space: nowrap;
  }
}
</style>
