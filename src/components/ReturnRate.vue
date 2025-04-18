<template>
  <div class="returnRate-wrap">
    <h4>收益率计算公式：</h4>
    <p>每日收益 / n万 x 365 / 100</p>
    <h4>实际年化收益率：</h4>
    <p><b>持有天数</b> = 当天日期 - 买入日期 - 2天</p>
    <p><b>实际年化收益率</b> = 总收益 / 持有天数 / n万 x 365 / 100</p>
    <h4>实际理财收益率：</h4>
    <el-form
      :inline="false"
      class="return-rate-form"
      :label-position="top"
      size="small"
    >
      <el-form-item label="单笔理财金额（元）">
        <el-input
          v-model="totalAmount"
          placeholder="请输入理财金额"
          type="number"
        ></el-input>
      </el-form-item>
      <el-form-item label="单笔理财总收益（元）">
        <el-input
          v-model="totalEarnings"
          placeholder="请输入理财收益"
          type="number"
        ></el-input>
      </el-form-item>
      <el-form-item label="买入日期">
        <el-date-picker
          v-model="purchaseDate"
          style="width: 100%"
          type="date"
          placeholder="选择买入日期"
          format="yyyy-MM-dd"
          value-format="yyyy-MM-dd"
        >
        </el-date-picker>
      </el-form-item>
      <el-form-item class="btn-container">
        <el-button
          type="primary"
          @click="calculateReturnRate"
          icon="el-icon-edit"
          >计算</el-button
        >
        <el-button icon="el-icon-refresh" @click="reset">重置</el-button>
      </el-form-item>
    </el-form>
    <div v-if="calculatedRate" class="result">
      <p>实际年化收益率：{{ calculatedRate }}%</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "returnRate",
  components: {},
  props: {},
  data() {
    return {
      totalAmount: "",
      purchaseDate: "",
      totalEarnings: "",
      calculatedRate: null,
    };
  },
  computed: {},
  mounted() {},
  methods: {
    calculateReturnRate() {
      // 这里实现计算收益率的逻辑
      if (!this.totalAmount || !this.purchaseDate || !this.totalEarnings) {
        this.$message.warning("请填写完整的理财金额、理财收益和买入日期");
        return;
      }

      // 示例计算逻辑，根据实际需求修改
      const today = new Date();
      const purchaseDate = new Date(this.purchaseDate);
      const diffTime = Math.abs(today - purchaseDate);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)) - 2; // 持有天数

      if (diffDays <= 0) {
        this.$message.warning("持有天数必须大于0");
        return;
      }

      const totalEarnings = parseFloat(this.totalEarnings); // 使用用户输入的实际收益
      const amount = parseFloat(this.totalAmount) / 10000; // 转换为n万

      // 实际年化收益率 = 总收益 / 持有天数 / n万 x 365 / 100
      const rate = (((totalEarnings / diffDays / amount) * 365) / 100).toFixed(
        2
      );
      this.calculatedRate = rate;
    },
    reset() {
      // 重置表单数据和计算结果
      this.totalAmount = "";
      this.purchaseDate = "";
      this.totalEarnings = "";
      this.calculatedRate = null;
    },
  },
  watch: {},
};
</script>

<style scoped lang="scss">
.returnRate-wrap {
  h4:not(:first-child) {
    margin: 30px 0 0;
  }
  p {
    font-size: 13px;
  }
  .return-rate-form {
    margin: 10px 0;

    .btn-container {
      display: flex;
      justify-content: center;
      margin-top: 10px;
    }
  }

  .result {
    margin-top: 40px;
    font-size: 24px;
    color: #409eff;
    font-weight: bold;
    p {
      font-size: inherit;
    }
  }
}
</style>
