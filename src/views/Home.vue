<template>
  <div class="home">
    <h2>任务计划列表</h2>
    <div class="wrapper">
      <div class="inner">
       <div class="add">
          <span>添加任务：</span>
        <div>
          <input type="text" class="task" v-model="currentTask" @keyup.enter="enter">
          <p>共有{{this.prolist.length}}个任务,{{noend == 0 ? "全部完成了":"完成了"+(this.prolist.length - noend)+"个,还有"+noend+"个" }}</p>
        </div>
       </div>
        
        <div class="list">
          <span>任务列表:</span>
          <div>
            <input type="radio" name="choosetype" id="choosetype1" checked="true" @click="chooseType(1)"><label for="choosetype1">全部任务</label>
            <input type="radio" name="choosetype" id="choosetype2" @click="chooseType(2)"><label for="choosetype2">已完成任务</label>
            <input type="radio" name="choosetype" id="choosetype3" @click="chooseType(3)"><label for="choosetype3">未完成任务</label>
          </div>
          <ul class="list-ul">
            <li v-for="(item,index) in newList" :key="index" @mouseover="mouseOver" @mouseleave="mouseLeave" :class="{'editing':curindex === index}">
              <div class="view">
                <input type="checkbox" class="checkd" @click="item.isCompleted = !item.isCompleted" v-model="item.isCompleted">  
                <span class="edit" @dblclick="curindex = index" >{{item.title}}</span>
                <button class="delete" v-show="itemIsDelect" @click="deleteItem(item)">x</button>
              </div>
              
                <input type="text" class="text" v-model="item.title" @keyup.enter="edited" 
                @keyup.esc="cancleEdit(item)"
                @focus="beforeEdit(item.title)"
                @blur="edited">
             
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  
 
  name: 'Home',
  components: {
    
  },
  data(){
    
   
    return {
      //任务input
      currentTask:'',
      prolist:[],          //使用prolist记录所有的数据  newlist经过filter然后用来展示v-for渲染的内容 
      newList:[],
      itemIsDelect:false,     
      curindex:null,           //用来记录选中哪一项 给 li 动态添加class
      beforeEditText:''   ,    
      
    }
  },
  watch:{
    prolist:{
            handler:function(){
                this.saveList("storeData",this.prolist);
            },
            deep:true
        }

  },
  methods:{
    saveList(key,value){
      localStorage.setItem(key,JSON.stringify(value))
    },
    fetchList(key){
      return JSON.parse(localStorage.getItem(key))
    },
    //输入框按下回车 
    enter(){
      if(this.currentTask == '') return
        this.prolist.unshift({
          title:this.currentTask,
          isCompleted:false
        })
        this.currentTask = ''
        this.chooseType(1)
        
        
    },
    //三个选项选中哪个分别传入 1 2 3 分别代表 全部任务  完成任务  未完成任务  
    chooseType(type){
      switch(type){
        case 1 : this.newList = this.prolist;
        break;

        case 2 : this.newList = this.prolist.filter(function(item){return item.isCompleted});  
        break;

        case 3 : this.newList = this.prolist.filter(function(item){return !item.isCompleted});
        break;
      }
    },
    //鼠标移入列表项
    mouseOver(){
      this.itemIsDelect = true
      
    },
  //鼠标离开列表项
    mouseLeave(){
      this.itemIsDelect = false
    },
    //删除每一项绑定按钮
    deleteItem(item){
      let index = this.prolist.indexOf(item)
      this.prolist.splice(index,1)
      
    },
    //完成编辑
    edited(){
      this.curindex = ''
    },
  //esc 取消编辑 传来item
    cancleEdit(item){
      item.title = this.beforeEditText
      this.curindex = ''
    },
    //编辑之前先保存没有改变的
    beforeEdit(title){
      this.beforeEditText = title
    }
  },
  computed:{
    //计算未完成的任务
    noend(){
      
      return this.prolist.filter(item => {
        return !item.isCompleted
      }).length
    }
  },
   mounted(){
      this.prolist = this.fetchList('storeData')
      this.newList = this.prolist

    },
}
</script>

<style scoped>
  * {
    margin: 0;
    padding: 0;
  }
 
  h2 {
    background-color: red;
    text-align: center;
  }
  .wrapper {
    display: flex;
    justify-content: center;
  }
  .inner {
    width: 600px;
  }
  .task {
    width: 100%;
    
    height: 30px;
    margin-top: 30px;
  }
  .list-ul {
    margin-top: 30px;
    display: flex;
    flex-direction: column;
  }
  .inner {
    margin-top: 30px;
  }
  .add {
    margin-top: 30px;
  }

  .list {
    margin-top: 30px;
  }

  .view {
    display: flex;
    align-items: center;
  }
  .list-ul > li {
    display: flex;
    height: 50px;
    align-items: center;
    border-top:1px solid #ccc;
    border-bottom:1px solid #ccc;
    margin-top: 10px;
  }
  .checkd {
    display:inline-block;
    width: 40px;
    height: 40px;
    border-radius: 50%;
  }
 
  .edit {
    width: 500px;
    height: 30px;
    border: 0;
    text-align: center;
  }
  .delete {
    background-color: skyblue;
    width: 30px;
    height: 30px;
    margin-left: 20px;
  }
  .list-ul .editing .view {
    display: none;
  }
  .text {
    width: 500px;
    height: 30px;
    padding: 0 10px;
    display: none;
  }
  .list-ul .editing .text {
    display: block;
  }
  
</style>
