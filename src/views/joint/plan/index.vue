<template>
  <div slot="body" class="container offset-bottom">
    <div  v-if="isfolw==true" style="height:100%;padding-top:44px;">
      <iframe :src="flowSrc" style="height:100%; width:100%;margin:0;border:0;"> </iframe>
    </div>
  </div>
</template>


<script>
// 先注释
// import  {yascTest} from '@/api/joint/joint'

// window.open(`/templates/optionalModule.html`);
export default {
  methods:{
    async getData(){
      // let {code,data} = await  yascTest();
      // if(code != 200){
      //   return
      // }
      // let str = JSON.stringify(data);
      // localStorage.setItem('tascTestData',str);
      window.open(`/templates/optionalModule.html`);
    }
  },

  data() {
    return {
      flowSrc: null,
    }
  },
  created() {
    this.getData();

  },


}
</script>
