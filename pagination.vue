<template>
  <ul :class="$style['pagination']">
    <li
      :class="[value <= 1 && $style['disable']]"
      @click="value > 1 && (value -= 1)"
    >
      ＜
    </li>
    <li v-if="firstPage !== false" @click="value = firstPage">
      {{ firstPage }}
    </li>
    <li
      v-if="jumpPrev !== false"
      @click="value = jumpPrev"
      :class="$style['jump']"
    >
      •••
    </li>
    <li
      v-for="(page, index) of pages"
      :key="index"
      :class="[$style['pages-item'], value == page && $style['active']]"
      @click="value = page"
    >
      {{ page }}
    </li>
    <li
      v-if="jumpNext !== false"
      @click="value = jumpNext"
      :class="$style['jump']"
    >
      •••
    </li>
    <li v-if="lastPage !== false" @click="value = lastPage">
      {{ lastPage }}
    </li>
    <li
      :class="[value < pageCount || $style['disable']]"
      @click="value < pageCount && (value += 1)"
    >
      ＞
    </li>
  </ul>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  props: {
    modelValue: {
      type: Number,
      default: 1,
    },
    total: {
      type: Number,
      default: 1,
    },
    size: {
      type: Number,
      default: 1,
    },
  },
  computed: {
    value: {
      get() {
        return (this as any).modelValue;
      },
      set(value: number) {
        this.$emit("update:modelValue", value);
      },
    },
    pages() {
      const { value, pageCount } = this as any;

      let base = Math.max(1, value - 1);
      let limit = Math.min(pageCount + 1, base + 3);

      return new Array(limit - base).fill(base).map((b, i) => b + i);
    },
    firstPage(): false | number {
      const { value } = this as any;
      return value > 2 && 1;
    },
    lastPage(): false | number {
      const { value, pageCount } = this as any;
      return pageCount - value > 1 && pageCount;
    },
    jumpNext(): false | number {
      const { value, pageCount } = this as any;
      return pageCount - Math.max(value, 2) > 2 && value + 3;
    },
    jumpPrev(): false | number {
      const { value } = this as any;
      return value > 3 && value - 3;
    },
    pageCount() {
      const { total, size } = this as any;
      return Math.ceil(total / size) || 1;
    },
  },
});
</script>
<style lang="less" module>
.pagination {
  display: block;
  text-align: center;
  padding: 0;
  @media (max-width: 700px) {
    .pages-item:not(.active) {
      display: none;
    }
  }

  & > * {
    display: inline-block;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: white;
    color: #444;
    font-family: Arial, Helvetica, sans-serif;
    font-size: 16px;
    line-height: 40px;
    cursor: pointer;
    margin: 0 6px;
    box-shadow: 0 0 2px #666 inset;
    transition: all 0.3s;

    &.jump {
      background: none !important;
      box-shadow: none !important;
    }
    &.disable {
      color: #ddd;
      cursor: not-allowed;
    }
    &.active {
      background: #0974fa;
      color: white;
    }
    &:not(.disable):hover {
      color: #0974fa;
      box-shadow: 0 0 2px #0974fa inset;
    }
  }
}
</style>
