
<template>
  <section class="index container">
    <div class="left-bar" :style="{left: isLeftbar ? 0 : '-249px'}">
      <div class="title">
        <img class="logo" src="/favicon.ico">
        <span>猿梦极客导航</span>
      </div>
      <el-row>
        <el-col :span="24">
          <el-menu
            :default-active="active"
            class="el-menu-vertical-demo"
            background-color="#30333c"
            text-color="#6b7386"
            active-text-color="#fff"
          >
            <el-menu-item
              :index="index"
              v-for="(item,index) in data"
              :key="item._id"
              @click="jump(index)"
            >
              <i :class="item.icon"></i>
              <span slot="title">{{item.classify}}</span>
            </el-menu-item>
          </el-menu>
        </el-col>
      </el-row>
    </div>
    <section class="main">
      <div id="mainContent">
        <!-- 手机端菜单 -->
        <div id="menu-box">
          <div id="menu">
            <input type="checkbox" id="menu-form">
            <label for="menu-form" class="menu-spin" @click="isLeftbar=!isLeftbar">
              <div class="line diagonal line-1"></div>
              <div class="line horizontal"></div>
              <div class="line diagonal line-2"></div>
            </label>
          </div>
        </div>
        <!-- 开发社区 -->
        <div class="box" v-for="(item,index) in data" :key="index" :ref="`box-${index}`">
          <a href="#" :name="item.classify"></a>
          <div class="sub-category">
            <div>
              <i :class="item.icon" class="icon"></i>
              {{item.classify}}
            </div>
          </div>
          <NavItem :data="sub" v-for="(sub,idx) in item.sites" :key="'sub-'+idx"/>
        </div>
      </div>
      <footer class="footer">
        <div class="copyright">
          <div>
            Copyright © 2018- 2050
            <a href="http://geekape.github.io">极客猿梦导航 钟储兵博客</a>
          </div>
        </div>
      </footer>
      <back-top/>
    </section>
    <add-nav-btn :data="data"/>
  </section>
</template>

<script>
import BackTop from "@/components/BackTop";
import AddNavBtn from "@/components/AddNavBtn";
import NavItem from "@/components/NavItem";
export default {
  data() {
    return {
      active: "0",
      data: [],
      scroll: 0,
      selfIndex: 0,
      isLeftbar: true
    };
  },
  components: {
    BackTop,
    AddNavBtn,
    NavItem
  },
  watch: {
    active () {
      const leftBarTop = document.querySelector('.left-bar').scrollTop
      const num = this.active
      const length = this.data.length
      if (num >= 10 && num <= length) {
        document.querySelector('.left-bar').scrollTop = leftBarTop + 60
      } 
      if (num < 10 && num >= 0) {
        document.querySelector('.left-bar').scrollTop = leftBarTop - 60
      }
    }
  },
  methods: {
    jump(idx) {
      this.selfIndex = idx;

      // 跳转
      let allSite = document.querySelectorAll(".box");
      let selfOffsetTop = allSite[idx].offsetTop - 10;
      // 判断是否是在手机上
      window.screenWidth = document.body.clientWidth;
      if (window.screenWidth < 481) {
        selfOffsetTop -= 50;
      }
      // Chrome
      document.body.scrollTop = selfOffsetTop;
      // Firefox
      document.documentElement.scrollTop = selfOffsetTop;
      // Safari
      window.pageYOffset = selfOffsetTop;
    },

    async getData() {
      const res = await this.$api.getHome();
      this.data = res.data;
    },
    dataScroll() {
      const that = this;
      let scrollTop =
        document.documentElement.scrollTop || document.body.scrollTop;
      let allSite = document.querySelectorAll(".box");
      for (let i = 0; i < allSite.length; i++) {
        if (scrollTop >= allSite[i].offsetTop) {
          that.selfIndex = i;
        }
      }
    },
    handleScroll () {
      const that = this
      const length = that.data.length
      const content = document.querySelectorAll('.box')
      const top = document.body.scrollTop || document.documentElement.scrollTop
      for (let i=length-1; i>=0; i--) {
        if (top >= content[i].offsetTop - 20) {
          that.active = i.toString()
          break
        }
      }
    }
  },
  mounted (){
    window.addEventListener('scroll', this.handleScroll);
  },
  created() {
    const that = this;
    this.getData();
    // window.addEventListener('scroll', this.dataScroll);
    window.onresize = () => {
      return (() => {
        window.screenWidth = document.body.clientWidth;
        if (window.screenWidth < 481) {
          that.isLeftbar = false;
        } else {
          that.isLeftbar = true;
        }
      })();
    };
    window.onresize();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.el-dialog {
  min-width: 320px;
}

.logo {
  width: 20px;
  height: 20px;
  margin-right: 8px;
}
</style>
