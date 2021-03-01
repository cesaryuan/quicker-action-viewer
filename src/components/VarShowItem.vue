<template>
  <span
    ><el-image
      class="varListItem"
      :src="`./assets/varPic/${varTypeDict[variable.Type].toLowerCase()}.png`"
      fit="fill"
      :lazy="false"
    ></el-image>
    {{ variable.Key }}</span
  >
</template>

<script>
import varTypeDict from "../assets/varTypeDict.json";
export default {
  props: ["variable"],
  data() {
    return {
      varTypeDict,
    };
  },
};
</script>

<style scoped>
.varListItem {
  height: 1.2em;
  width: 1.2em;
  position: relative;
  top: 0.2em;
}

span {
  font-size: 12px;
}
</style>