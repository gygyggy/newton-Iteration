<template>
  <div>
    <el-alert title="按F12查看运行过程" type="success" :closable="false"/>
    <div style="margin-top: 20px">简单迭代 Demo:</div>
    <el-row>
      <el-button type="primary" v-for="(item,index) in fucList" @click="switchSimpleDome(item,index)" :key="index">
        {{ item }}
      </el-button>
    </el-row>
    <el-descriptions title="参数:" style="margin-top: 20px" :border="true" size="mini">
      <el-descriptions-item label="起始迭代点">
        <div>
          <el-input-number
              v-model="start"
              :controls="false"
              placeholder="Please input x0"
          />
        </div>
      </el-descriptions-item>
      <el-descriptions-item label="最大迭代次数">
        <div>
          <el-input-number
              v-model="max_iter"
              :min="1"
              :max="10000"
              controls-position="right"
              placeholder="Please input max_iter"
          />
        </div>
      </el-descriptions-item>
      <el-descriptions-item label="精度(小数点后n位)">
        <div>
          <el-input-number
              v-model="num"
              placeholder="Please input epsilon"
              :min="1"
              :max="10000"
              controls-position="right"
          />
        </div>
      </el-descriptions-item>
    </el-descriptions>
    <div>output：{{ output }}</div>
  </div>
</template>

<script>
import {Parser} from "expr-eval";
import {iteration} from "@/core/Iteration";


const parser = new Parser()
parser.consts.e = parser.consts.E
parser.consts.pi = parser.consts.PI

export default {
  name: 'Iteration',
  props: {
    msg: String
  },
  data() {
    return {
      func: 'x^2-3*x+2-e^x',
      simpleFunc: '',
      start: 1,
      max_iter: 100,
      num: 8,
      fucList: ['x^2-3*x+2-e^x', 'x^3+2*x^2+10*x-20'],
      simpleFuncList: ['(-e^x+x^2+2)/3', '(20-2*x^2)/(x^2+10)'],
    }
  },
  computed: {
    epsilon() {
      return Math.pow(10, -this.num);
    },
    simpleExpr() {
      let res = ''
      try {
        res = parser.parse(this.simpleFunc)
      } catch (_e) {
        // console.log(e)
      }
      return res
    },
    simpleExprFun() {
      let res = () => 0
      try {
        res = this.simpleExpr.toJSFunction('x')
      } catch (_e) {
        // console.log(e)
      }
      return res
    },
    output() {
      return iteration(this.simpleExprFun, this.start, this.max_iter, this.epsilon)
    }
  },
  methods: {
    switchSimpleDome(exp, index) {
      this.methodSwitch = false
      this.func = exp
      this.simpleFunc = this.simpleFuncList[index]
    }
  },
}
</script>

<style scoped>
.prefix {
  color: black;
}
</style>
