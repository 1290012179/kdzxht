<template>
  <!-- 头 -->
  <div class="app">
   <div class="header">
     <div class="title">看点资讯精选</div>
     <div class="photo">
       <img src="" alt="">
     </div>
     
   </div>
   <div class="center">
     <div class="left-nav">
      <ul>
        <router-link to='/'>
           <li :class="{current:this.currentRoute=='/'}">
            <i class="fa fa-tv"></i>
            首页
            <i class="fa fa-angle-right"></i>
          </li>

        </router-link>
        <router-link to='/category'>  
          <li :class="{current:this.currentRoute=='/category'}">
            <i class="fa fa-navicon"></i>
            栏目管理
            <i class="fa fa-angle-right"></i>
          </li>
        </router-link>
        <router-link to='/article'>
        <li :class="{current:this.currentRoute=='/article'}">
          <i class="fa fa-file-text"></i>
          文章管理
          <i class="fa fa-angle-right"></i>
        </li>
        </router-link>
         <router-link to='/user'>
          <li :class="{current:this.currentRoute=='/user'}">
            <i class="fa fa-user-circle"></i>
           用户管理
            <i class="fa fa-angle-right"></i>
          </li>
        </router-link>
        <router-link to='/setting'>
          <li :class="{current:this.currentRoute=='/setting'}">
            <i class="fa fa-cog"></i>
            系统设置
            <i class="fa fa-angle-right"></i>
          </li>
        </router-link>
      </ul>
                    
     </div>
     <div class="content">
      <div class="wrapper"><router-view/></div>
       
     </div>
   </div>
  </div>
</template>

<script>
export default {
  data (){
    return{
      currentRoute:'/article'
    }
  },watch:{
      '$route':function(to,from){
        this.currentRoute = to.path;
      }
    },
    mounted(){
      this.$router.push(this.currentRoute);
    }
}
</script>

<style>
  html{
    font:normal normal 12px '微软雅黑','Microsoft YaHei';
    color: #666;
  }
  body,ul,ol,dl,p,h1,h2,h3{
    margin: 0;
    padding: 0;
  }
  ul,ol{
    list-style: none;
  }
  a{
    color: #666;
    text-decoration: none;
  }
  div{
    box-sizing: border-box;
  }
  .header{
    position: absolute;
    top: 0;
    width: 100%;
    height: 60;
    background-color: teal;
    padding: 0 1em;
  }
  .header .title{
    color: #fff;
    line-height: 60px;
    font-size: 18px;
  }
  .center{
    position: absolute;
    top: 60px;
    bottom: 0;
    width: 100%;
    background-color:#f0f0f0; 
  }
  .center .left-nav{
    width: 190px;
    height: 100%;
    float:left;
    background-color:#fff; 
  }
  .center .left-nav>ul li{
    line-height: 2.5em;
    border-bottom: 1px solid #ccc;
    text-align: center;
    position: relative;
  }
  .center .left-nav>ul li.current{
    background-color: #f0f0f0;
  }
  .center .left-nav>ul li i.fa{
    position: absolute;
    top: 50%;
    margin-top: -.5em;
  }
  .center .left-nav>ul li i.fa:first-child{
    left: 3em;
  }
  .center .left-nav>ul li i.fa:last-child{
    right: 1em;
  }
  .center .content{
    margin-left: 190px;
    height: 100%;
    padding: 1em 1em 0 1em;

  }
  .center .content .wrapper{
    width: 100%;
    height: 100%;
    background-color: #fff;
    border-radius: 5px;
    padding:.5em;
  }
</style>
