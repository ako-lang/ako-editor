<template>
  <div id="app">
    <div v-for="stack of stacks" v-bind:key="`stack_${stack.name}`" style="padding-bottom: 25px">
      <Command
        :index="0"
        :total="stack.length"
        :data="stack"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Command from "./components/Command.vue";
import { toAst } from "ako-lang/dist/ako-web";

@Component({
  components: { Command },
})
export default class App extends Vue {
  stacks: any[] = [];

  async mounted() {
    await this.loadFile();
  }

  async loadFile() {
    const code = `
name = "World"
for item := [5,4,3,2,1,0] {
  @sleep(1)
  if item > 0 {
    @print("{item} ...")
  }
}
@print("Hello {name} !")
    `;
    const res: any = toAst(code);
    console.log(res);
    this.stacks.push({
      type: 'Stack',
      name: "Main",
      commands: JSON.parse(JSON.stringify(res)),
    });
    this.stacks.push({
      type: 'Stack',
      name: "Secondary",
      commands: JSON.parse(JSON.stringify(res)),
    });
  }
}
</script>

<style lang="scss">
html, body {
  padding: 0;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;

  background: rgb(186, 195, 232)
}
</style>
