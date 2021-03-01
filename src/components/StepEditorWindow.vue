<template>
  <el-dialog title="动作编辑框" :visible.sync="dialogVisible" width="70%">
    <el-collapse :value="['输入参数', '输出参数']"> 

      <el-collapse-item title="输入参数" name="输入参数">
        <el-form label-width="100px" :model="step.InputParams">
          <template v-for="(value, name) in step.InputParams">
            <el-form-item :label="name" :key="name" size="mini">
              <el-select
                v-model="step.InputParams[name].VarKey"
                placeholder="--值或表达式--"
                @clear="step.InputParams[name].VarKey = null"
                clearable
              >
                <el-option
                  v-for="item in variables"
                  :key="item.Key"
                  :label="item.Key"
                  :value="item.Key"
                >
                  <var-show-item :variable="item"></var-show-item>
                </el-option>
              </el-select>
              <el-input
                v-if="step.InputParams[name].VarKey === null"
                v-model="step.InputParams[name].Value"
                clearable
              ></el-input>
            </el-form-item>
          </template>
        </el-form>
      </el-collapse-item>

      <el-collapse-item title="输出参数" name="输出参数">
        <el-form label-width="100px" :model="step.OutputParams">
          <template v-for="(value, name) in step.OutputParams">
            <el-form-item :label="name" :key="name" size="mini">
              <el-select
                v-model="step.OutputParams[name]"
                placeholder="--值或表达式--"
                @clear="step.OutputParams[name] = null"
                clearable
              >
                <el-option
                  v-for="item in variables"
                  :key="item.Key"
                  :label="item.Key"
                  :value="item.Key"
                >
                  <var-show-item :variable="item"></var-show-item>
                </el-option>
              </el-select>
            </el-form-item>
          </template>
        </el-form>
      </el-collapse-item>
    </el-collapse>

    <span slot="footer">
      <el-button type="primary" @click="save">保存</el-button>
    </span>
  </el-dialog>
</template>

<script>
import VarShowItem from "./VarShowItem.vue";
export default {
  name: "StepEditor",
  components: {
    VarShowItem,
  },
  props: {
    showStepEditor: Boolean,
    editStep: Object,
    variables: Array,
  },
  data() {
    return {
      dialogVisible: this.showStepEditor,
      step: JSON.parse(JSON.stringify(this.editStep)),
    };
  },
  methods: {
    save() {
      Object.assign(this.editStep, this.step);
      this.dialogVisible = false;
      // this.$emit("close", this.visible);
    },
  },
  watch: {
    dialogVisible() {
      this.$emit("update:showStepEditor", this.dialogVisible);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.el-select {
  display: block;
  position: relative;
  width: 40%;
}
.el-form-item__label {
  font-size: 12px;
}
</style>
