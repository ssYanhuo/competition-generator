<template>
  <div id="background-container">
    <v-container class=" white--text">
      <v-row>
        <v-col cols="12">
          <h1 class="my-16 text-h2" @click="fullscreen">{{ competitionName }}</h1>
        </v-col>
      </v-row>
      <v-row>
        <v-col class="my-16" cols="5">
          <p class="text-center font-weight-black" style="font-size: 8em">{{ selectedA }}</p>
        </v-col>
        <v-col class="my-16" cols="2">
          <p class="text-center font-weight-black" style="font-size: 8em">:</p>
        </v-col>
        <v-col class="my-16" cols="5">
          <p class="text-center font-weight-black" style="font-size: 8em">{{ selectedB }}</p>
        </v-col>
      </v-row>
      <v-row justify="center">
        <v-col cols="2" align="center">
          <v-btn outlined x-large color="white" @click="startGenerate" :disabled="disableStartBtn">生成</v-btn>
        </v-col>
        <v-col cols="2" align="center">
          <v-btn outlined x-large color="white" @click="stopGenerate" :disabled="disableStopBtn">停止</v-btn>
        </v-col>
        <v-col cols="2" align="center">
          <v-btn outlined x-large color="white" @click="showConfigDialog = true">设置</v-btn>
        </v-col>
      </v-row>
    </v-container>
    <v-dialog v-model="showConfigDialog" width="500px">
      <v-card>
        <v-card-title class="text-h5">设置</v-card-title>
        <v-card-text class="mt-8">
          <v-text-field v-model="competitionName" label="比赛名称" outlined/>
          <v-text-field v-model="teamAMembersRaw" label="队伍A成员（用空格分割）" outlined/>
          <v-text-field v-model="teamBMembersRaw" label="队伍B成员（用空格分割）" outlined/>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="changeConfig">好</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="showAlertDialog" width="500px">
      <v-card>
        <v-card-title class="text-h5">{{ alertDialogTitle }}</v-card-title>
        <v-card-text class="mt-8">
          {{ alertDialogContent }}
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="showAlertDialog = false">好</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>

</template>

<script>
export default {
  name: 'Generator',

  data: () => ({
    showConfigDialog: false,
    showAlertDialog: false,
    alertDialogTitle: '',
    alertDialogContent: '',
    teamAMembers: [0, 1, 2, 3, 4],
    teamBMembers: [5, 6, 7 ,8, 9],
    teamAMembersRaw: '0 1 2 3 4',
    teamBMembersRaw: '5 6 7 8 9',
    selectedA: '?',
    selectedB: '?',
    selectedAIndex: '?',
    selectedBIndex: '?',
    competitionName: '比赛名称',
    isGenerating: false,
    intervalId: 0,
    disableStartBtn: false,
    disableStopBtn: true
  }),
  methods: {
    fullscreen: function () {
      let docElm = document.documentElement;
      if (docElm.requestFullscreen) {
        docElm.requestFullscreen();
      }
      else if (docElm.msRequestFullscreen) {
        docElm.msRequestFullscreen();
      }
      else if (docElm.mozRequestFullScreen) {
        docElm.mozRequestFullScreen();
      }
      else if (docElm.webkitRequestFullScreen) {
        docElm.webkitRequestFullScreen();
      }
    },
    rand: function (minNum, maxNum) {
      switch (arguments.length) {
        case 1:
          return parseInt(Math.random() * minNum + 1, 10);
        case 2:
          return parseInt(Math.random() * (maxNum - minNum + 1) + minNum, 10);
        default:
          return 0;
      }
    },
    changeConfig: function () {
      let that = this
      that.showConfigDialog = false
      that.teamAMembers = that.teamAMembersRaw.split(' ')
      that.teamBMembers = that.teamBMembersRaw.split(' ')
    },
    generate: function (isManual) {
      let that = this
      if(isManual && that.isGenerating){
        clearInterval(that.intervalId)
        return
      }
      let lenA = that.teamAMembers.length
      let lenB = that.teamBMembers.length
      that.selectedAIndex = that.rand(0, lenA - 1)
      that.selectedBIndex = that.rand(0, lenB - 1)
      that.selectedA = that.teamAMembers[that.selectedAIndex]
      that.selectedB = that.teamBMembers[that.selectedBIndex]

    },
    startGenerate: function (){
      let that = this
      if (that.teamAMembers.length !== that.teamBMembers.length){
        that.showAlertDialog = true
        that.alertDialogTitle = '错误'
        that.alertDialogContent = '双方队伍人数不相等'
        return
      }
      if (that.teamAMembers.length === 0 || that.teamBMembers.length === 0){
        that.showAlertDialog = true
        that.alertDialogTitle = '提示'
        that.alertDialogContent = '已完成全部抽选'
        return
      }
      that.disableStartBtn = true
      that.disableStopBtn = false
      that.isGenerating = true
      that.intervalId = setInterval(that.generate, 20)
    },
    stopGenerate: function (){
      let that = this
      that.disableStartBtn = false
      that.disableStopBtn = true
      that.isGenerating = false
      clearInterval(that.intervalId)
      that.teamAMembers.splice(that.selectedAIndex, 1)
      that.teamBMembers.splice(that.selectedBIndex, 1)
      console.log(that.teamAMembers)
      console.log(that.teamBMembers)
    }
  }
}
</script>

<style>
#background-container {
  background-image: url("../assets/background.png");
  background-size: cover;
  height: 100%;
  width: 100%;
}
</style>