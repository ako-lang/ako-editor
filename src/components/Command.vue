<template>
  <div v-if="data.type === 'Stack'">
    <draggable
      class="blocks"
      tag="ul"
      v-model="data.commands"
      v-bind="dragOptions"
      handle=".handle"
    >

    <Block
        slot="header"
        ref="block"
        :index="0"
        :total="total"
        :hasInput="false"
        :hasOutput="true"
        v-bind:key="`${data.name}_task_header`" style=""
    >
        <div class="block-icon"><i class="lni lni-flag-alt"/></div>
        <div class="block-label">{{ data.name }}</div>
        <div class="block-handler"></div>
    </Block>

    <Command
        :index="index + 1"
        :total="data.commands.length"
        :data="command"
        v-for="(command, index) in data.commands"
        v-bind:key="`${data.name}_${index}_${command.type}`"
    />
    </draggable>
  </div>
  <Block
    ref="block"
    :index="index"
    :total="total"
    v-else-if="data.type === 'LoopFor'"
    :hasContent="true"
  >
    <div class="block-icon"><i class="lni lni-infinite"></i></div>
    <div class="block-label">{{ data.type }}</div>
    <div class="block-handler"><i class="lni lni-line-double handle"></i></div>

    <template v-slot:block>
      <draggable
        class="blocks sub-blocks"
        tag="ul"
        v-model="data.block.statements"
        v-bind="dragOptions"
        handle=".handle"
      >
        <Command
            :index="index2"
            :total="data.block.statements"
            :data="command"
            v-for="(command, index2) in data.block.statements"
            v-bind:key="`${index2}_${command.type}`"
        />
      </draggable>
    </template>
  </Block>
  <Block
    ref="block"
    :index="index"
    :total="total"
    v-else-if="data.type === 'If'"
    :hasContent="true"
  >
    <div class="block-icon"><i class="lni lni-direction-alt"></i></div>
    <div class="block-label" style="flex-grow: 1">{{ data.type }}</div>
    <div class="block-handler"><i class="lni lni-line-double handle"></i></div>
    
    <template v-slot:block>
      <draggable
        class="blocks sub-blocks"
        tag="ul"
        v-model="data.ifBlock.statement"
        v-bind="dragOptions"
        handle=".handle"
      >
        <Command
        :index="index2"
        :total="data.ifBlock.statements.length"
        :data="command"
        v-for="(command, index2) in data.ifBlock.statements"
        v-bind:key="`${index2}_${command.type}`"
        />
      </draggable>
    </template>
  </Block>
  <Block :index="index" :total="total" v-else-if="data.type === 'Assign'" ref="block">
    <div class="block-icon"><i class="lni lni-shift-left"></i></div>
    <div class="block-label">{{ data.type }}</div>
    <div class="block-handler"><i class="lni lni-line-double handle"></i></div>
  </Block>
  <Block :index="index" :total="total" v-else-if="data.type === 'Task'" ref="block">
    <div class="block-icon"><i class="lni lni-chevron-right-circle"></i></div>
    <div class="block-label">{{ data.type }}</div>
    <div class="block-handler"><i class="lni lni-line-double handle"></i></div>
  </Block>
  <Block :index="index" :total="total" v-else ref="block">
    <div class="block-icon"></div>
    <div class="block-label">{{ data.type }}</div>
    <div class="block-handler"><i class="lni lni-line-double handle"></i></div>
  </Block>
</template>

<style lang="sass" scoped>
.blocks
    list-style: none
    padding: 20px
    margin: 0px

    .block-icon
        width: 30px

    .block-label
        flex-grow: 1
    
    .block-handler
        width: 30px
        cursor: move

    &.sub-blocks
        padding: 0
</style>

<script lang="ts">
import draggable from "vuedraggable";
import Block from "./Block.vue";
import { Component, Prop, Vue, Watch } from "vue-property-decorator";

@Component({
  components: { Block, draggable },
})
export default class Command extends Vue {
  @Prop() private index!: number;
  @Prop() private total!: number;
  @Prop() data!: any;

  @Watch("data")
  onDataChanged() {
    if (!this.$refs.block) return;
    this.$refs.block.resize();
  }

  get dragOptions() {
    return {
      animation: 200,
      group: "description",
      disabled: false,
      ghostClass: "ghost",
    };
  }
}
</script>
