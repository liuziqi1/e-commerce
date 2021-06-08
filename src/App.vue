<template>
  <div id="app">
    <!-- //模板中必须要有根标签 -->

    <router-view> </router-view>
    <!-- //路由占位符，用来显示对应路由所匹配的组件 -->
  </div>
</template>

<script>
export default {
  name: "app",
};
</script>



<style lang="less" scoped>
</style>