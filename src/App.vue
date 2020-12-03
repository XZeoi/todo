<template>
  <div id="app">
    <a-input placeholder="请输入任务" class="my_ipt" :value="inputValue"
    @change="handleInputChange"/>
    <a-button type="primary" @click="addItemToList">添加事项</a-button>

    <a-list bordered :dataSource="infoList" class="dt_list">
      <a-list-item slot="renderItem" slot-scope="item">
        <!-- 复选框 -->
        <a-checkbox :checked="item.done" @change="(e)=>{cbStatusChanged(e,item.id)}">{{item.info}}</a-checkbox>
        <!-- 删除链接 -->
        <a slot="actions" @click="removeItemById(item.id)">删除</a>
      </a-list-item>

      <!-- footer区域 -->
      <div slot="footer" class="footer">
        <!-- 未完成的任务个数 -->
        <span>{{unDoneLength}}条剩余</span>
        <!-- 操作按钮 -->
        <a-button-group>
          <a-button :type="viewKey === 'all' ? 'primary' : 'default'" @click="changeList('all')">全部</a-button>
          <a-button :type="viewKey === 'undone' ? 'primary' : 'default'" @click="changeList('undone')">未完成</a-button>
          <a-button :type="viewKey === 'done' ? 'primary' : 'default'" @click="changeList('done')">已完成</a-button>
        </a-button-group>
        <!-- 把已经完成的任务清空 -->
        <a @click="clean">清除已完成</a>
      </div>
    </a-list>
  </div>
</template>

<script>
import { mapState, mapMutations,mapGetters } from 'vuex'

export default {
  name: 'app',
  data() {
    return {
      
    }
  },
  created(){
    this.$store.dispatch('getList')
  },
  computed: {
    ...mapState(['inputValue','viewKey']),
    ...mapGetters(['unDoneLength','infoList'])
  },
  methods: {
    ...mapMutations(['setInputValue','addItem','removeItem']),
    handleInputChange(e){
      console.log(e.target.value)
      this.setInputValue(e.target.value)
    },
    addItemToList(){
      if(this.inputValue.trim().length <= 0){
        return this.$message.warning("不能为空")
      }
      this.addItem()
    },
    removeItemById(id){
      this.removeItem(id)
    },
    //？？？在change事件中要传递id，所以用箭头函数？？？
    cbStatusChanged(e,id){
      //通过e.target.checked 可以接受到最新的选中状态
      const param = {
        id:id,
        status:e.target.checked
      }
      this.$store.commit('changeStatus',param)
    },
    clean(){
      this.$store.commit('cleanDone')
    },
    changeList(key){
      this.$store.commit('changeViewKey', key)
    }
  }
}
</script>

<style scoped>
#app {
  padding: 10px;
}

.my_ipt {
  width: 500px;
  margin-right: 10px;
}

.dt_list {
  width: 500px;
  margin-top: 10px;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
