<template>
  <div id="app">
    <router-view />
    <notifications />
  </div>
</template>

<style lang="scss">
html,
body,
#admin {
  font-family: sans-serif;
  background-color: #ffffff;
  height: 100%;
  width: 100%;
  margin: 0px;
  padding: 0px;
  .bulma {
    // We are not changing anything currently. If need to customize then import bulma saas.
    @import '../node_modules/bulma/css/bulma.css';
    * {
      box-sizing: border-box;
    }
  }
}
</style>
