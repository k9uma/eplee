<template>
	<div id="app" ref="app" class="default">
		<router-view v-loading="!isReady" />
	</div>
</template>

<script>
import fs from 'fs';
import { addToDB } from '../shared/dbUtilis.js';

export default {
  name: 'App',
  data() {
    return {
      isReady: false,
      theme: 'light'
    };
  },

  mounted() {
    // check last argv has path to epub or not
    const arg = process.argv[process.argv.length - 1];

    if (arg.endsWith('.epub') && fs.existsSync(arg)) {
      // if epub file is passed in args open file and redirect to reader
      const file = arg;

      addToDB(file, this.$db, (info)=>{
        this.isReady = true;
        this.$router.push({ name: 'Reader', params: { id: info.id } })
      });
    }
    else{
        this.isReady = true;
    }
    
    this.$bus.on('theme-change',(theme)=>{
      this.$refs.app.className = "";
      this.$refs.app.classList.add(theme);

      this.$store.commit('setTheme', theme);
    });
  },
};
</script>

<style lang="scss" scoped>
@import './assets/style';

#app{
  width: 100%;
  height: 100%;
  border-radius: $border-radius;
}

</style>


<style lang="scss">
::-webkit-scrollbar {
  display: none;
}

html,
body {
  margin: 0px;
  width: 100%;
  height: 100%;  
  -webkit-font-smoothing: antialiased;
  overflow: hidden;
}

.el-container {
  position: absolute;
  top: 0px;
  bottom: 0px;
  right: 0px;
  left: 0px;
}

.el-main {
  width: 100%;
  height: 100%;
  padding: 0px;
}

.el-button {
  border: none;
}

.el-table , .el-table *, .el-radio-button__inner,
.el-tree, .el-button {
background: inherit !important;
color: inherit !important;
}

.default{
  background: #fff;
  color: #555;
}

.dark{
  background: #444;
  color: #eee;
}

.tan{
  background: #fdf6e3;
  color: #002b36;
}

</style>
