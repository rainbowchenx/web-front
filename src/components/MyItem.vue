<template>
  <transition name = 'todo'>
          <li>
            <label>
                <input type="checkbox" :checked="todo.done" @change = 'handleCheck(todo.id)'/>
                <span v-show = '!todo.isEdit'> {{ todo.title }}</span>
                <input 
                v-show = 'todo.isEdit' 
                type="text" 
                :value = "todo.title" 
                @blur="handleBlur(todo,$event)"
                ref = 'inputTitle'
                >
            </label>
        <button class="btn btn-primary" @click =' handleDelete(todo.id)'>删除</button>
        <button v-show = "!todo.isEdit" class="btn btn-danger" @click ='handleEdit(todo)'>编辑</button>
    </li>
  </transition>

</template>

<script>
import PubSub from 'pubsub-js';
export default {
    name:'MyItem',
    props:['todo'],
    methods:{
      handleCheck(id){
        // this.checkTodo(id)
        this.$bus.$emit('checkTodo',id)
      },
      handleDelete(id){
        if(confirm('are you sure to delete')){
          // this.deleteTodo(id)
          // 全局总线
          // this.$bus.$emit('deleteTodo',id)
          PubSub.publish('deleteTodo',id)
        }
      },
      handleEdit(todo){
        if(todo.hasOwnProperty.call('isEdit')){
          todo.isEdit = true
        }else{
          this.$set(todo,'isEdit',true)
        }
        this.$nextTick(function(){
          this.$refs.inputTitle.focus()
        })
        
        
        // todo.isEdit = true
      },
      // 失去焦点回调
      handleBlur(todo,e){
        todo.isEdit = false
        if(!e.target.value.trim()) return alert('输入不可为空')
        this.$bus.$emit('updateTodo',todo.id,e.target.value)
      }
    }

}
</script>

<style>

/*item*/
li {
  list-style: none;
  height: 36px;
  line-height: 36px;
  padding: 0 5px;
  border-bottom: 1px solid #ddd;
}

li label {
  float: left;
  cursor: pointer;
}

li label li input {
  vertical-align: middle;
  margin-right: 6px;
  position: relative;
  top: -1px;
}

li button {
  float: right;
  
  margin-top: 3px;
  display: none;
}

li:before {
  content: initial;
}

li:last-child {
  border-bottom: none;
}

li:hover{
  background-color: beige;

}

li:hover button{
  display: block;

}

</style>