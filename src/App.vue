<template>
  <div id="app">
    <nav>
      <router-link to="/dept">部门信息管理</router-link> |
      <router-link to="/employee">员工信息管理</router-link> |
      <router-link to="/kpi">员工考核信息管理</router-link> |
      <router-link to="/ana">数据分析管理</router-link>
    </nav>
    <router-view/>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
