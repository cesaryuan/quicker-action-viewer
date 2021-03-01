<template>
  <div id="app">
    <el-container>
      <el-header height="">
        <el-row :gutter="20">
          <el-col :span="20" :offset="0">
            <el-input
              v-model="userInput"
              placeholder="https://getquicker.net/sharedaction?code=××××-××××-××××-××××-××××××××××××"
              size="normal"
              clearable
            ></el-input
          ></el-col>
          <el-col :span="4" :offset="0"
            ><el-button
              type="primary"
              size="default"
              @click="action = userInput"
              >加载</el-button
            ></el-col
          >
        </el-row>
      </el-header>

      <el-main height="">
        <el-row :gutter="20">
          <el-col :xs="0" :sm="0" :md="0" :lg="0" :xl="0"> </el-col>

          <el-col :xs="16" :sm="18" :md="18" :lg="18" :xl="18">
            <el-tree
              :data="xaction.Steps"
              :props="defaultProps"
              default-expand-all
              :expand-on-click-node="false"
              v-loading="loading"
              empty-text="没有正在编辑的动作，请点击加载"
            >
              <span
                class="custom-tree-node"
                slot-scope="{ node, data }"
                @dblclick="editPanel(data)"
              >
                <span class="stepName">{{ stepNameDict[node.label] }}</span>
                <span class="stepNote">{{ data.Note }}</span>
              </span>
            </el-tree>
          </el-col>

          <el-col :xs="0" :sm="6" :md="6" :lg="6" :xl="6">
            <el-table :data="xaction.Variables" size="mini">
              <el-table-column label="变量名" width="150">
                <template v-slot="{ row }">
                  <var-show-item :variable="row"></var-show-item>
                </template>
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>
      </el-main>
    </el-container>

    <step-editor
      :editStep="editingStep"
      v-if="showStepEditor"
      :showStepEditor.sync="showStepEditor"
      :variables="xaction.Variables"
      @close="showStepEditor = false"
      aaa="daw"
    ></step-editor>
  </div>
</template>

<script>
import axios from "axios";
import stepNameDict from "./assets/quickerStepData.json";
import StepEditorWindow from "./components/StepEditorWindow.vue";
import VarShowItem from "./components/VarShowItem.vue";

export default {
  name: "app",
  components: {
    "step-editor": StepEditorWindow,
    VarShowItem: VarShowItem,
  },
  data() {
    return {
      xaction: {},
      actionItem: {},
      defaultProps: {
        children: "IfSteps",
        label: "StepRunnerKey",
      },
      stepNameDict: stepNameDict,
      editingStep: {},
      showStepEditor: false,
      action:
        "",
      loading: false,
      userInput: "https://getquicker.net/sharedaction?code=ca4f5811-1a94-4c9e-c8bc-08d8642b4c39"
    };
  },
  methods: {
    getActionData(id) {
      if (id === null) {
        this.$cmessage({
          message: "输入的链接/ID格式不正确",
          type: "warning",
          offset: 50
        });
        return;
      }
      this.loading = true;
      var link =
        "https://quicker.cesaryuan.workers.dev/?option=getActionItem&id=" + id;
      this.getActionItemByLink(link)
        .then((data) => {
          this.$cmessage({
            message: "获取成功",
            type: "success",
          });
          this.loading = false;
          this.xaction = this.getXAciton(data);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    editPanel(step) {
      this.editingStep = step;
      this.showStepEditor = true;
    },
    getActionItemByLink: async function (link) {
      var res = await axios.get(link);
      let actionitem = res.data.data;
      if (actionitem == null) {
        this.$cmessage({
          message: res.data.message,
          type: "warning",
        });
        throw "获取动作定义出错";
      }
      return actionitem;
    },
    getXAciton: function (actionItem) {
      let xAction = JSON.parse(actionItem.data);
      return xAction;
    },
  },
  computed: {
    actionId() {
      var m = /\w{8}-\w{4}-\w{4}-\w{4}-\w{12}/.exec(this.action);
      if (m) return m[0];
      else return null;
    },
  },
  watch: {
    action() {
      // _.debounce(this.getActionData, 500)(this.actionId);
      this.getActionData(this.actionId);
    },
  },
  created() {
    this.$cmessage = function(obj){
      obj.offset = 50;
      obj.duration = 1200;
      this.$message(obj);
    }
  },
};
</script>

<style>

span.stepNote {
  color: burlywood;
  font-size: small;
  margin-left: 10px;
}

.el-tree-node {
  margin: 5px 0;
}

.custom-tree-node {
  /* flex: 1;
  display: flex;
  align-items: center;
  justify-content: space-between; */
  font-size: 14px;
  padding-right: 8px;
}
</style>
