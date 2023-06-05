<template>
  <div class="app">
    <div class="main" v-if="list">
      <div
        :id="'mychart' + i"
        v-for="(val, i) in list"
        :key="i"
        style="width: 50%; height: 50vh"
      ></div>
    </div>
    <div v-else class="text">暂无数据~~</div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import {
  listRole,
  getRole,
  delRole,
  addRole,
  updateRole,
  cityList,
  typeListUp,
  publisher,
} from "@/api/decisions/graphic";
export default {
  name: "graphic",
  props: [""],
  components: {},
  data() {
    return {
      // 查询参数
      queryParams: {},
      cityList: [],
      list: [],
    };
  },
  computed: {},
  watch: {
    // list: {
    //   handler(newname, oldname) {
    //     this.handMain();
    //   },
    //   deep: true,
    // },
  },
  created() {
    this.getList();
  },
  mounted() {},
  methods: {
    /** 搜索按钮操作 */
    handleQuery() {
      this.getList();
    },
    getList() {
      listRole({ id: this.$route.query.id }).then((response) => {
        let list = JSON.parse(response.data.contrastData);
        this.$set(this, "list", list);
        this.$nextTick(() => {
          this.handMain();
        });
      });
    },
    handMain() {
      let _this = this;
      _this.list.forEach((val, i) => {
        let name = "mychart" + i;
        let mychart = echarts.init(document.getElementById(name));
        let option = {
          color: ['#a90000'],
          title: {
            text: val.name,
          },
          xAxis: {
            type: "category",
            data: val.title,
          },
          yAxis: {
            type: "value",
          },
          tooltip: {
            trigger: "axis",
            axisPointer: {
              type: "shadow",
            },
          },
          series: [
            {
              data: val.data,
              type: "bar",
            },
          ],
        };

        mychart.setOption(option);
        window.addEventListener("resize", function () {
          mychart.resize();
        });
        // mychart.resize();
      });
    },
  },
};
</script>

<style scoped lang="scss">
.app {
  padding: 18px;
  #mychart {
  }
  .main {
    display: flex;
    flex-wrap: wrap;
  }
  .text {
    color: #ccc;
    text-align: center;
    font-size: 30px;
  }
}
</style>
