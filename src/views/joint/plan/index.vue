<template>
<!--  <div slot="body" class="container offset-bottom">-->
<!--    <div  v-if="isfolw==true" style="height:100%;padding-top:44px;">-->
<!--      <iframe :src="flowSrc" style="height:100%; width:100%;margin:0;border:0;"> </iframe>-->
<!--    </div>-->
<!--  </div>-->
  <div class="app-container">
    <div v-if="is_select_category">
    <h1 style="text-align: center">欢迎您使用联防联控预案生成系统</h1>
    <el-row :gutter="100">
      <el-col :span="8">
        <el-card :body-style="{ padding: '0px' }">
          <img src="../../../assets/plan_generation/flood_cover.jpg" alt="洪水预案封面" class="cover_picture">
          <h2 style="text-align: center">洪涝灾害预案</h2>
          <p style="color: #99a9bf">主要用于应对强降雨、冰雪融化等原因引起的水位上涨、山洪爆发等事件</p>
          <el-button type="primary" style="margin-left: 120px" @click="choose_city_flood">生成该预案</el-button>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card :body-style="{ padding: '0px' }">
          <img src="../../../assets/plan_generation/transport_cover.jpg" alt="交通运输预案封面" class="cover_picture">
          <h2 style="text-align: center">交通运输预案</h2>
          <p style="color: #99a9bf">主要用于应对公路危险货物运输、重点物资运输等公路交通事件</p>
          <el-button type="primary" style="margin-left: 120px">生成该预案</el-button>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card :body-style="{ padding: '0px' }">
          <img src="../../../assets/plan_generation/covid_cover.jpg" alt="防疫安全预案封面" class="cover_picture">
          <h2 style="text-align: center">防疫安全预案</h2>
          <p style="color: #99a9bf">主要用于2020年突然爆发的新冠疫情的应对及其他各类防疫工作</p>
          <el-button type="primary" style="margin-left: 120px">生成该预案</el-button>
        </el-card>
      </el-col>
    </el-row>
    </div>
    <div v-if="is_select_flood_city">
      <h1 style="text-align: center">请选择您要使用的预案模板</h1>
      <el-row :gutter="100">
        <el-col :span="8">
          <el-card :body-style="{ padding: '0px' }">
            <img src="../../../assets/plan_generation/flood_cover_nanjing.jpg" alt="洪水预案封面" class="cover_picture">
            <h2 style="text-align: center">城市：南京市</h2>
            <p style="color: #99a9bf;text-align: center">《2020年南京市防汛预案》</p>
            <el-button type="primary" style="margin-left: 120px" @click="choose_flood_nanjing">选择该预案</el-button>
          </el-card>
        </el-col>
        <el-col :span="8">
          <el-card :body-style="{ padding: '0px' }">
            <img src="../../../assets/plan_generation/flood_cover_shenzhen.jpg" alt="交通运输预案封面" class="cover_picture">
            <h2 style="text-align: center">城市：深圳市</h2>
            <p style="color: #99a9bf;text-align: center">《2019年深圳市防汛预案》</p>
            <el-button type="primary" style="margin-left: 120px">选择该预案</el-button>
          </el-card>
        </el-col>
        <el-col :span="8">
          <el-card :body-style="{ padding: '0px' }">
            <img src="../../../assets/plan_generation/flood_cover_zhuhai.jpg" alt="防疫安全预案封面" class="cover_picture">
            <h2 style="text-align: center">城市：珠海市</h2>
            <p style="color: #99a9bf;text-align: center">《2009年珠海市防汛应急工作预案》</p>
            <el-button type="primary" style="margin-left: 120px">选择该预案</el-button>
          </el-card>
        </el-col>
      </el-row>
      <el-button type="primary" style="margin-left: 45%;margin-top: 100px" @click="return_choose_category">返回上一级</el-button>
    </div>
    <div v-if="is_select_flood_nanjing">
      <flood_nanjing></flood_nanjing>
    </div>
  </div>
</template>


<script>
// 先注释
// import  {yascTest} from '@/api/joint/joint'
import flood_nanjing from '@/views/joint/plan/flood_nanjing.vue'


// window.open(`/templates/optionalModule.html`);
export default {
  components: { flood_nanjing },
  // components: { Flood_nanjing },
  methods:{
    choose_city_flood()//显示要显示洪水预案的城市
    {
      this.is_select_category=false;
      this.is_select_flood_city=true;
    },
    return_choose_category()//从预案城市选择部分返回到选择预案类型模块
    {
      this.is_select_category=true;
      this.is_select_flood_city=false;
    },
    choose_flood_nanjing()//选择南京市的防控预案
    {
      this.is_select_flood_city=false;
      this.is_select_category=false;//双重保险，保证只有南京市的页面是会被显示的
      this.is_select_flood_nanjing=true;
    },
    async getData(){
      // let {code,data} = await  yascTest();
      // if(code != 200){
      //   return
      // }
      // let str = JSON.stringify(data);
      // localStorage.setItem('tascTestData',str);
      //  window.open(`/templates/optionalModule.html`);
      // window.open(`/templates/dragTemplateCataGZTransportation.html`);
    }
  },

  data() {
    return {
      flowSrc: null,
      is_select_category:true,
      is_select_flood_city:false,
      is_select_flood_nanjing:false,
    }
  },
  created() {
    this.getData();

  },


}
</script>

<style>
.cover_picture{
  width: 400px;
  height: 200px;
}
</style>
