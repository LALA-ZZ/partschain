<template>

  <el-menu class="el-menu-vertical-demo" background-color="#304156" text-color="#fff" active-text-color="#ffd04b"
    :collapse="isCollapse" :unique-opened="true" :collapse-transition="false" :default-active="activePath">
    <!-- <h3 v-show="!isCollapse">售后配件多级仓库协同管理系统</h3>
    <h3 v-show="isCollapse">配件</h3> -->
    <!-- <el-submenu :index="item.path" v-for="item in asideMenu" :key="item.path" @click="clickMenu(item)"> -->

    <!-- <el-menu-item :index="item.path" v-for="item in noChildren" :key="item.path" @click="clickMenu(item)">
      <template slot="title">
        <i class="el-icon-menu"></i>
        <span>{{item.label}}</span>
      </template>
    </el-menu-item>
    <el-submenu :index="item.path" v-for="item in hasChildren" :key="item.path" @click="clickMenu(item)">
      <template slot="title">
        <i :class="'el-icon-s-' + item.icon"></i>
        <span>{{item.label}}</span>
      </template>

      <el-menu-item :index="subItem.path" v-for="subItem in item.children" :key="subItem.path"
        @click="clickMenu(subItem)">
        <template slot="title">
          <i class="el-icon-menu"></i>
          <span>{{subItem.label}}</span>
        </template>
      </el-menu-item>
    </el-submenu> -->

    <el-submenu :index="item.path" v-for="item in menu" :key="item.path" @click="clickMenu(item)">
      <template slot="title">
        <i :class="'el-icon-s-' + item.icon"></i>
        <span>{{item.label}}</span>
      </template>

      <el-menu-item :index="subItem.path" v-for="subItem in item.children" :key="subItem.path"
        @click="clickMenu(subItem)">
        <template slot="title">
          <i class="el-icon-menu"></i>
          <span>{{subItem.label}}</span>
        </template>
      </el-menu-item>

    </el-submenu>
  </el-menu>

</template>

<script>
export default {
  name: "CommonAside",
  data () {
    return {
      // asideMenu: [//静态的菜单目录

      //   {
      //     path: '/',
      //     name: 'home',
      //     label: '首页',
      //     icon: 'home'
      //   },
      //   {
      //     path: '/baseinfomation',
      //     name: 'baseinfomation',
      //     label: '基础信息管理',
      //     icon: 'data',
      //     children: [
      //       {
      //         path: '/eresourcemanage',
      //         name: 'eresourcemanage',
      //         label: '企业资源',
      //         icon: 'data',
      //       },
      //       {
      //         path: '/partsinfomanage',
      //         name: 'partsinfomanage',
      //         label: '配件信息',
      //         icon: 'data',
      //       },
      //       {
      //         path: '/warehouseinfomanage',
      //         name: 'warehouseinfomanage',
      //         label: '仓库信息',
      //         icon: 'data',
      //       },
      //       {
      //         path: '/forecastInfomanage',
      //         name: 'forecastInfomanage',
      //         label: '预测信息',
      //         icon: 'data',
      //       },
      //       {
      //         path: '/optimizationinfomanage',
      //         name: 'optimizationinfomanage',
      //         label: '优化信息',
      //         icon: 'data',
      //       },
      //     ]
      //   },
      //   {
      //     path: '/storage',
      //     name: 'storage',
      //     label: '入库管理',
      //     icon: 'management',
      //     children: [
      //       {
      //         path: '/partsapply',
      //         name: 'partsapply',
      //         label: '配件申请',
      //         icon: 'management',
      //       },
      //       {
      //         path: '/applycheck',
      //         name: 'applycheck',
      //         label: '申请审批',
      //         icon: 'management',
      //       },
      //       {
      //         path: '/partsentry',
      //         name: 'partsentry',
      //         label: '配件入库',
      //         icon: 'management',
      //       },
      //     ]
      //   },
      //   {
      //     path: '/instock',
      //     name: 'instock',
      //     label: '在库管理',
      //     icon: 'management',
      //     children: [
      //       {
      //         path: '/partsamountverification',
      //         name: 'partsamountverification',
      //         label: '库存盘点',
      //         icon: 'management',
      //       },
      //       {
      //         path: '/partsamountwarnning',
      //         name: 'partsamountwarnning',
      //         label: '库存预警',
      //         icon: 'management',
      //       },
      //     ]
      //   },
      //   {
      //     path: '/putstorage',
      //     name: 'putstorage',
      //     label: '出库管理',
      //     icon: 'management',
      //     children: [
      //       {
      //         path: '/outapply',
      //         name: 'outapply',
      //         label: '出库申请',
      //         icon: 'management',
      //       },
      //       {
      //         path: '/outcheck',
      //         name: 'outcheck',
      //         label: '出库审批',
      //         icon: 'management',
      //       },
      //       {
      //         path: '/partsout',
      //         name: 'partsout',
      //         label: '配件出库',
      //         icon: 'management',
      //       },
      //     ]
      //   },
      //   {
      //     path: '/partsDemandforecast',
      //     name: 'partsDemandforecast',
      //     label: '配件需求预测',
      //     icon: 'opportunity'
      //   },
      //   {
      //     path: '/inventoryoptimization',
      //     name: 'inventoryoptimization',
      //     label: '安全库存优化',
      //     icon: 'opportunity'
      //   },
      // ],
      activePath: ''
    }
  },
  created () {
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  computed: {//计算属性
    //判断菜单是否有子菜单
    // noChildren () {
    //   return this.asideMenu.filter(item => !item.children);
    // },
    // hasChildren () {
    //   return this.asideMenu.filter(item => item.children);
    // },
    noChildren () {
      return this.menu.filter(item => !item.children);
    },
    hasChildren () {
      return this.menu.filter(item => item.children);
    },
    isCollapse () {
      return this.$store.state.tab.isCollapse;
    },
    menu () {//动态菜单
      return this.$store.state.tab.menu
    }
  },
  methods: {
    //点击左侧菜单，面包屑联动
    clickMenu (item) {
      console.log(item.name)
      this.$router.push({ name: item.name });//在选择菜单之前，先路由跳转到对应的组件
      this.$store.commit('selectMenu', item);
      //保存链接的激活状态
      window.sessionStorage.setItem('activePath', item.path)
      this.activePath = item.path

    }
  }
}
</script>

<style scoped>
.el-menu {
  height: 100%;
  border: none;
}
h3 {
  color: #ffffff;
  text-align: center;
  line-height: 20px;
  font-size: 16px;
}
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  min-height: 400px;
}
</style>