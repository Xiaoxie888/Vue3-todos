<template>
  <section class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input class="new-todo" placeholder="What needs to be done?" autofocus 
      @keyup.enter="hAdd"
      v-model="addname"
      />
    </header>

    <section class="main">
      <input id="toggle-all" class="toggle-all" type="checkbox"  v-model='isAll'/>
      <label for="toggle-all">Mark all as complete</label>
      <ul class="todo-list">
        <li  v-for="item in renderList " :key="item"  >
          <div class="view">
            <input class="toggle" type="checkbox" checked  v-model='item.flag'/>
            <label>{{item.name}}</label>
            <button @click="hDel(item.id)" class="destroy"></button>
          </div>
          <input class="edit" value="Create a TodoMVC template" />
        </li>
    
      </ul>
    </section>

    <footer class="footer"  v-if="isShowFooter">
      <span class="todo-count"><strong>{{leftCounts}}</strong> item left</span>
      <ul class="filters">
        <li v-for="item in tabs" :key="item" @click="active = item" >
          <a :class="{selected :item === active}" href="#/">{{item}}</a>
        </li>
        
      </ul>
      <button class="clear-completed" v-if="isShowClear" @click="clearCompleted">Clear completed</button>
    </footer>
  </section>
</template>
<script>

import { computed, reactive,toRefs ,watch} from "vue"
export default{
  setup(){
    const state = reactive({
       list: JSON.parse(localStorage.getItem('shuju')) || [
          { id: 1, name: '吃饭', flag: true },
          { id: 2, name: '睡觉', flag: false },
          { id: 3, name: '打豆豆', flag: true }
        ],
        addname:'',//添加名字
        tabs: ['all', 'active', 'completed'],//状态
        active: localStorage.getItem('active') || 'all'//此时被点击

    })
    //删除
   const  hDel = (id)=>{
      const index = state.list.findIndex(item=>item.id === id)
      state.list.splice(index,1)

    }
    //添加
    const hAdd = ()=>{
      if(state.addname.trim().length === 0 ) return
      state.list.unshift({
        id:+new Date(),
        name:state.addname,
        flag:false
      })
      state.addname=''
    }
    // 左下方数据显示剩余数量
    const leftCounts = computed( () => {
      //计算属性需要return
    return  state.list.filter(item=> !item.flag).length
    })
    
    //底部栏的显示与否
    const isShowFooter = computed( ()=> {
      return !!state.list.length
    })
    //底部栏的清除已完成的显示
    const isShowClear = computed ( () => {
      return state.list.some(item=> item.flag)
    })
    //清除已完成
    const clearCompleted =() => {
     state.list = state.list.filter(item => !item.flag)
    }
    //点击全部，未完成，已完成的切换
    const renderList = computed ( ()=>{
       if(state.active === 'completed'){
        return state.list.filter(item => item.flag)
      }
      if(state.active === 'active'){
        return state.list.filter(item => !item.flag)
      }else{
        return state.list
      }
    })
    //单选和全选
    const isAll = computed ({
      get(){
       return  state.list.every(item => item.flag)
      },
      set(val){
        return state.list.forEach(item => item.flag = val)
      }
    })
    //页面缓存
    watch(()=>state.list,(newValue)=>{
      localStorage.setItem('shuju',JSON.stringify(newValue))
    },
    {
      deep:true
    }
    )
    //选择页面缓存
    watch(()=>state.active,(newP)=>{
      localStorage.setItem('active',newP)
    })

    return{
      ...toRefs(state),
      hDel,
      hAdd,
      leftCounts,
      isShowFooter,
      isShowClear,
      clearCompleted,
      renderList,
      isAll,
      
      }
  }
}
</script>