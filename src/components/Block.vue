<template>
  <li class="block" :style="`width: ${width}px;`">
    <svg class="block-shape" :width="width" :height="height + 6">
      <path :d="svgPath" :fill="color" stroke-width="0.25" stroke="#666666" />
    </svg>
    <div class="block-inside" ref="container">
      <div
        class="block-content"
        :style="`line-height: ${$slots.block ? 20 : height}px; display: flex; flex-direction: row`"
      >
        <slot></slot>
      </div>
      <ul class="block-task" v-if="$slots.block">
        <slot name="block"></slot>
      </ul>
    </div>
  </li>
</template>

<style scoped lang="sass">
.draggable
    cursor: move
    user-select: none

.block
    position: relative

.block-inside
    position: relative
    .block-content
        // user-select: none
        text-align: left
        padding: 0 10px
        font-weight: 600
        font-family: sans-serif
        letter-spacing: -0.5px
        font-size: 90%

    .block-task
        padding-bottom: 30px
        list-style: none

.block-shape
    filter: drop-shadow( 2px 2px 6px rgba(0, 0, 0, 0.25))
    position: absolute
    top: 0
    left: 0
    // z-index: 1
</style>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class Command extends Vue {
  @Prop({ default: 0 }) index!: number;
  @Prop({ default: 1 }) total!: number;
  width = 240;
  height = 40;
  @Prop({ default: false }) hasContent!: boolean;
  @Prop({ default: true }) hasInput!: boolean;
  @Prop({ default: true }) hasOutput!: boolean;
  color = "#FF0000";

  colors = [
    "#FFADAD",
    "#FFD6A5",
    "#FDFFB6",
    "#CAFFBF",
    "#9BF6FF",
    "#A0C4FF",
    "#BDB2FF",
    "#FFC6FF",
    "#FFFFFC",
  ];

  mounted() {
    this.color = this.colors[Math.floor(Math.random() * this.colors.length)];
    this.resize()
  }

  resize() {
    if (this.$slots.block) {
      this.height = (this.$refs.container as any).clientHeight + 1;
    }
  }

  get svgPath() {
    const entries: string[] = [];
    const input = this.hasInput ? "q 10 10 20 0" : "h 20";
    const output = this.hasOutput ? "q -10 10 -20 0" : "h -20";
    const first = this.index === 0;
    const last = this.index >= this.total;

    entries.push("m 20 0");
    entries.push(input);
    let vertTopOffset = 0;
    let vertBottomOffset = last ? -10 : 0;

    if (!first) {
      entries.push(`h ${this.width - 40}`);
    } else {
      entries.push(`h ${this.width - 50}`);
      entries.push(`q 10 0 10 10`);
      vertTopOffset = -10;
    }

    if (!this.hasContent) {
      entries.push(`v ${this.height + vertTopOffset + vertBottomOffset}`);
    } else {
      entries.push(`v ${20 + vertTopOffset}`);
      entries.push(`h -${this.width - 80}`);
      entries.push("q -10 10 -20 0");
      entries.push("h -24");
      entries.push(`v ${this.height - 40}`);
      entries.push("h 4");
      entries.push(`h ${this.width - 40}`);
      entries.push(`v ${20 + vertBottomOffset}`);
    }

    if (last) {
        entries.push(`q 0 10 -10 10`);
    }

    entries.push(`h -${this.width - 40 + vertBottomOffset}`);
    entries.push(output);
    entries.push("h -20");
    entries.push(`v -${this.height}`);
    entries.push("h 20");

    return entries.join("");
  }
}
</script>