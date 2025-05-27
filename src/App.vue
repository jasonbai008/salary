<template>
  <div id="app">
    <el-tabs v-model="activeName" @tab-click="handleTabClick">
      <!-- 工资计算 -->
      <el-tab-pane label="工资计算" name="salary">
        <!-- 工资计算器 -->
        <SalaryCalculator />
      </el-tab-pane>
      <!-- 工资科普 -->
      <el-tab-pane label="工资科普" name="pic">
        <img src="./assets/img/tax.png" style="width: 100%" />
      </el-tab-pane>
      <!-- 利率计算 -->
      <el-tab-pane label="收益率计算" name="rate">
        <return-rate />
      </el-tab-pane>
      <!-- 关于我 -->
      <el-tab-pane label="关于我" name="about">
        <div class="wrap">
          <img
            src="./assets/img/profile.png"
            class="me"
            title="1105843831"
            @click="vx"
          />
          <p>小白</p>
        </div>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import SalaryCalculator from "./components/SalaryCalculator.vue";
import ReturnRate from "./components/ReturnRate.vue";

export default {
  name: "App",
  components: {
    SalaryCalculator,
    ReturnRate,
  },
  data() {
    return {
      activeName: "salary",
    };
  },
  // 组件挂载时检查URL参数
  mounted() {
    this.initTabFromUrl();
  },
  methods: {
    // 初始化选项卡：从URL参数中获取tab值
    initTabFromUrl() {
      const urlParams = new URLSearchParams(window.location.search);
      const tabFromUrl = urlParams.get('tab');
      
      // 如果URL中有tab参数且值有效，则使用URL中的值
      if (tabFromUrl && ['salary', 'pic', 'rate', 'about'].includes(tabFromUrl)) {
        this.activeName = tabFromUrl;
      }
    },
    
    // 处理选项卡点击事件：更新URL参数
    handleTabClick(tab) {
      const tabName = tab.name;
      
      // 更新URL参数，不刷新页面
      const url = new URL(window.location);
      url.searchParams.set('tab', tabName);
      window.history.pushState({}, '', url);
    },
    
    vx() {
      alert("1105843831");
    },
  },
};
</script>

<style lang="scss">
body {
  margin: 0;
  display: flex;
  justify-content: center;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  width: 100%;
  margin: 20px;
  max-width: 600px;
}
#pane-2 {
  display: flex;
  flex-direction: column;
}
.me {
  width: 100px;
  margin: 200px auto 10px;
  border-radius: 50%;
  box-shadow: 3px 3px 3px rgb(0 0 0 / 30%);
  &:hover {
    cursor: pointer;
  }
}
#pane-2 p {
  text-align: center;
}
.wrap {
  text-align: center;
}
</style>
