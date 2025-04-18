<template>
  <div class="salaryCalculator">
    <el-form ref="form" label-position="top" :model="form" size="small">
      <el-form-item label="税前月工资">
        <el-input-number v-model="form.salary" controls-position="right" :min="0" @change="syncBase"></el-input-number>
      </el-form-item>
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="公积金基数">
            <el-input-number v-model="form.gongjijin" controls-position="right" :min="0"></el-input-number>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="五险基数">
            <el-input-number v-model="form.wuxian" controls-position="right" :min="0"></el-input-number>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="6">
          <el-form-item label="公积金">
            <el-input v-model="form.ratio0">
              <template slot="append">%</template>
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="养老保险">
            <el-input v-model="form.ratio1">
              <template slot="append">%</template>
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="医疗保险">
            <el-input v-model="form.ratio2">
              <template slot="append">%</template>
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="工伤保险">
            <el-input v-model="form.ratio3">
              <template slot="append">%</template>
            </el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label="专项附加扣除">
        <el-input v-model="form.kouchu.zinv" placeholder="子女教育" clearable class="kouchu"></el-input>
        <el-input v-model="form.kouchu.jixu" placeholder="继续教育" clearable class="kouchu"></el-input>
        <el-input v-model="form.kouchu.dabing" placeholder="大病医疗" clearable class="kouchu"></el-input>
        <el-input v-model="form.kouchu.daikuan" placeholder="住房贷款" clearable class="kouchu"></el-input>
        <el-input v-model="form.kouchu.zufang" placeholder="住房租金" clearable class="kouchu"></el-input>
        <el-input v-model="form.kouchu.shanyang" placeholder="赡养老人" clearable class="kouchu"></el-input>
      </el-form-item>
      <el-form-item class="footer">
        <el-button type="primary" @click="onSubmit" icon="el-icon-edit">计算</el-button>
        <el-button icon="el-icon-refresh" @click="reset">重置</el-button>
      </el-form-item>
    </el-form>
    <!-- 结果展示 -->
    <el-card size="small">
      <p>平均每月税后工资：{{ result.salary }} 元</p>
      <p>平均每年税后工资：{{ result.salary * 12 }} 元</p>
      <el-divider></el-divider>
      <p>已到达的税率：{{ result.taxRate }} %</p>
      <p>平均每月缴税：{{ result.tax }} 元</p>
      <p>全年缴纳个税：{{ result.tax * 12 }} 元</p>
      <el-divider></el-divider>
      <p>每月扣缴三险：{{ result.wuxian }} 元</p>
      <p>每月扣公积金：{{ result.gongjijin / 2 }} 元</p>
      <el-divider></el-divider>
      <p>每月公积金入账：{{ result.gongjijin }} 元</p>
      <p>全年公积金入账：{{ result.gongjijin * 12 }} 元</p>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "salaryCalculator",
  data() {
    return {
      form: {
        salary: "",
        gongjijin: "",
        wuxian: "",
        ratio0: 12,
        ratio1: 8,
        ratio2: 2,
        ratio3: 0.5,
        kouchu: {
          zinv: "",
          jixu: "",
          dabing: "",
          daikuan: "",
          zufang: "",
          shanyang: "",
        },
      },
      result: {
        salary: 0,
        gongjijin: 0,
        wuxian: 0,
        tax: 0,
        taxRate: 0,
      },
    };
  },
  methods: {
    syncBase(v) {
      // 自动填充公积金基数和五险基数
      this.form.gongjijin = v;
      this.form.wuxian = v;
    },
    onSubmit() {
      // 免税基数
      let baseFreeTax = 5000 * 12;
      // 全年税前工资
      let totalSalary = this.form.salary * 12;
      // 全年公积金
      let totalGongjijin =
        ((this.form.gongjijin * this.form.ratio0) / 100) * 12;
      // 全年三险金额
      let totalWuxian =
        ((this.form.wuxian *
          (parseFloat(this.form.ratio1) +
            parseFloat(this.form.ratio2) +
            parseFloat(this.form.ratio3))) /
          100 +
          3) *
        12;
      // 全年专项扣除
      let totalKouchu = this.genKouchu() * 12;
      // 全年应纳税所得额
      let beforeTax =
        totalSalary - totalGongjijin - totalWuxian - baseFreeTax - totalKouchu;
      // 全年应缴个税
      let totalTax = this.genTax(beforeTax);

      // 全年税后工资/12
      this.result.salary = parseInt(
        (totalSalary - totalGongjijin - totalWuxian - totalTax) / 12
      );
      this.result.gongjijin = parseInt(totalGongjijin / 12) * 2;
      this.result.wuxian = parseInt(totalWuxian / 12);
      this.result.tax = parseInt(totalTax / 12);
    },
    // 计算全年所得税
    genTax(salaryBeforeTax) {
      // 全年应纳税所得额
      let b = salaryBeforeTax;
      if (b <= 0) {
        this.result.taxRate = 0;
        return 0;
      }
      // 全年应纳税所得额必须大于0，才走下面的逻辑
      let tax = 0;
      if (b <= 36000) {
        tax = b * 0.03;
        this.result.taxRate = 3;
      } else if (b > 36000 && b <= 144000) {
        tax = b * 0.1 - 2520;
        this.result.taxRate = 10;
      } else if (b > 144000 && b <= 300000) {
        tax = b * 0.2 - 16920;
        this.result.taxRate = 20;
      } else if (b > 300000 && b <= 420000) {
        tax = b * 0.25 - 31920;
        this.result.taxRate = 25;
      } else if (b > 420000 && b <= 660000) {
        tax = b * 0.3 - 52920;
        this.result.taxRate = 30;
      } else if (b > 660000 && b <= 960000) {
        tax = b * 0.35 - 85920;
        this.result.taxRate = 35;
      } else if (b > 960000) {
        tax = b * 0.45 - 181920;
        this.result.taxRate = 45;
      }
      return tax;
    },
    // 计算单月专项扣除
    genKouchu() {
      let kou = this.form.kouchu;
      let kouchu = 0;
      for (let k in kou) {
        if (kou[k] !== "") kouchu += parseInt(kou[k]);
      }
      return kouchu;
    },
    reset() {
      this.form = {
        salary: "",
        gongjijin: "",
        wuxian: "",
        ratio0: 12,
        ratio1: 8,
        ratio2: 2,
        ratio3: 0.5,
        kouchu: {
          zinv: "",
          jixu: "",
          dabing: "",
          daikuan: "",
          zufang: "",
          shanyang: "",
        },
      };
      this.result = {
        salary: 0,
        gongjijin: 0,
        wuxian: 0,
        tax: 0,
        taxRate: 0,
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.salaryCalculator {
  .el-input-number {
    width: 100%;
  }
  .kouchu {
    width: calc(33.33% - 10px);
    margin-right: 10px;
    margin-bottom: 10px;
  }
  .el-checkbox {
    margin-right: 0px;
    width: 33.33%;
  }
  .footer {
    margin: 25px 0;
  }
}
.el-input::v-deep .el-input-group__append {
  padding: 0 6px;
}
.footer::v-deep .el-form-item__content {
  text-align: center;
}
</style>
