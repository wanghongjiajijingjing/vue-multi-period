<template>
  <div class="multiWraper" v-on:click.stop >
    <div class="multiHeader" >
      <input type="text" :value="value" @click="periodClick(0)" />
      <span class="el-date-editor__trigger el-icon" :class="dateTag" @mousemove="periodTag(1)" @mouseout="periodTag(0)" @click.stop="periodClick(1)" ></span>
    </div>
    <div class="multiList" v-show="multiListTag" >
      <div class="multiListHd" >
        <button class="el-picker-panel__icon-btn el-icon-d-arrow-left" @click="switchYear(0)" ></button>
        <input class="multiYear" type="text" :value="currentYear" readOnly="readOnly" />
        <button class="el-picker-panel__icon-btn el-icon-d-arrow-right" @click="switchYear(1)" ></button>
      </div>
      <div class="multiListItems" >
        <!-- <span class="multiListItemLeft" >01</span>
        <span class="multiListItemCenter multiListSelected" >02</span>
        <span class="multiListItemRight" >03</span> -->
        <span v-for="date in dateList" :class="date.class+(date.tag==1?' multiListSelected':'')" @click="chooseMonth(date)" >{{date.name}}</span>
      </div>
      <div class="multiListBtns" >
        <button class="el-button el-button--primary el-button--small" @click="confirm" >确 定</button>
      </div>
    </div>
  </div>
</template>

<script>
  export default{
    name:'multiPeriod',
    props:{
      dataValue:{type:Object,default:{}},
      index:{type:String}
    },
    data () {
      return {
        value:'',
        dateObj:{},
        dateTag:'el-icon-date',
        currentYear:new Date().getFullYear(),
        dateList:[
          {name:'01',class:'multiListItemLeft',tag:0},
          {name:'02',class:'multiListItemCenter',tag:0},
          {name:'03',class:'multiListItemRight',tag:0},
          {name:'04',class:'multiListItemLeft',tag:0},
          {name:'05',class:'multiListItemCenter',tag:0},
          {name:'06',class:'multiListItemRight',tag:0},
          {name:'07',class:'multiListItemLeft',tag:0},
          {name:'08',class:'multiListItemCenter',tag:0},
          {name:'09',class:'multiListItemRight',tag:0},
          {name:'10',class:'multiListItemLeft',tag:0},
          {name:'11',class:'multiListItemCenter',tag:0},
          {name:'12',class:'multiListItemRight',tag:0}
        ],
        multiListTag:false
      }
    },
    watch:{
      currentYear (after,prev) {
        this.initYears(after,prev);
      }
    },
    methods:{
      periodTag (tag) {
        if(tag){
          if(this.value==""){
            this.dateTag="el-icon-date";
          }
          else{
            this.dateTag="el-icon-close";
          }
        }
        else{
          this.dateTag="el-icon-date";
        }
      },
      periodClick (tag) {
        if(!tag){
          this.initYears(this.currentYear);
          this.clearList();
          this.multiListTag=true;
        }
        else{
          if(this.dateTag=="el-icon-date"){
            this.initYears(this.currentYear);
            this.clearList();
            this.multiListTag=true;
          }
          else{
            this.value="";
            this.dateObj={};
            this.$emit("periodConfirm",this.index,this.dateObj);
            this.multiListTag=false;
          }
        }
      },
      clearList () {
        document.body.click();
        //window.dispatchEvent(new CustomEvent('click'));
      },
      chooseMonth (obj) {
        if(obj.tag){
          obj.tag=0;
        }
        else{
          obj.tag=1;
        }
      },
      switchYear (tag) {
        if(tag==1){
          this.currentYear++;
        }
        else{
          this.currentYear--;
        }
      },
      initYears (year,prev) {
        if(prev){
          this.dateObj[prev]=[];
          for(let i=0,len=this.dateList.length;i<len;i++){
            if(this.dateList[i].tag==1){
              this.dateObj[prev].push(this.dateList[i].name);
              this.dateList[i].tag=0;
            }
          }
        }
        else{
          for(let i=0,len=this.dateList.length;i<len;i++){
            if(this.dateList[i].tag==1){
              this.dateList[i].tag=0;
            }
          }
        }
        if(this.dateObj[year]){
          if(this.dateObj[year].length>0){
            for(let i=0,len=this.dateObj[year].length;i<len;i++){
              for(let j=0,lenJ=this.dateList.length;j<lenJ;j++){
                if(this.dateObj[year][i]==this.dateList[j].name){
                  this.dateList[j].tag=1;
                  break;
                }
              }
            }
          }
        }
        else{
          this.dateObj[year]=[];
        }
      },
      coalescingPeriods () {
        var str=[];
        for(var item in this.dateObj){
          if(this.dateObj[item].length>0){
            str.push(item+'-'+this.dateObj[item].toString());
          }
        }
        //this.dataValue=str.join(" ");
        this.value=str.join("、");
      },
      confirm () {
        this.dateObj[this.currentYear]=[];
        for(let j=0,lenJ=this.dateList.length;j<lenJ;j++){
          if(this.dateList[j].tag==1){
            this.dateObj[this.currentYear].push(this.dateList[j].name);
          }
        }
        this.coalescingPeriods();
        this.$emit("periodConfirm",this.index,this.dateObj);
        this.multiListTag=false;
      }
    },
    mounted:function () {
      /*var data={
          2016:['02','05','06'],
          2017:['03','07','11']
        };*/
      this.dateObj=this.dataValue;
      this.initYears(this.currentYear);
      this.coalescingPeriods();
      let This=this;
      window.addEventListener( "click",function(e){
        This.multiListTag=false;
      },false );
    }
  }
</script>

<style>
.multiWraper{
  position:relative;
}
.multiHeader{
  position:relative;
}
.multiHeader input{
  border: 1px solid #c0ccda;
  border-radius: 4px;
  line-height: 18px;
  width: 100%;
  color: #666;
  font-size: 13px;
  height: 30px;
  padding: 3px 5px;
  font-size: 12px;
  box-sizing: border-box;
}
.multiList{
  position:absolute;
  top:30px;
  right:0px;
  color: #475669;
  border: 1px solid #d3dce6;
  box-shadow: 0 2px 6px #ccc;
  background: #fff;
  border-radius: 2px;
  width:205px;
  z-index:1000;
}
.multiListHd{
  margin:10px 0px 10px 20px;
}
.multiListHd input{
  width:95px;
  text-align:center;
  font-size:14px;
  color:#475669;
  border:0px;
}
.multiYear{
  margin:0px 20px;
}
.multiListItems{

}
.multiListItems span{
  display:inline-block;
  width:32px;
  height:32px;
  line-height:32px;
  text-align:center;
  border-radius:2px;
  cursor:pointer;
  margin-top:5px;
}
.multiListItems span:hover{
  background-color:#d3ecff;
}
.multiListItems span.multiListSelected{
  color:#fff;
  background-color:#20a0ff;
}
.multiListItemLeft{
  margin-left:20px;
}
.multiListItemRight{

}
.multiListItemCenter{
  margin:0px 30px;
}
.multiListBtns{
  text-align:right;
  padding:5px 10px;
  border-top:1px solid #eee;
}
</style>
