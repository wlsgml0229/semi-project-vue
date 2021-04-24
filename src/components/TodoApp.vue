<template>
<div>
    <todo-item
      v-for="todo in todos"
      :key="todo.id"
      :todo="todo"
      @update-todo="updateTodo"
      @delete-todo="deleteTodo"
    />
     <!--todo-item 컴포넌트에서 todo를 받아서 처리하도록 할수 있도록  :todo="todo" todo데이터 바인딩-->
    <todo-creator @create-todo="createTodo" />
</div>
</template>

<script>
import lowdb from 'lowdb'
import LocalStorage from 'lowdb/adapters/LocalStorage'
import cryptoRandomString from 'crypto-random-string'
import _cloneDeep from 'lodash/cloneDeep'
import _find from 'lodash/find'
import _assign from 'lodash/assign'
import TodoCreator from './TodoCreator'
import TodoItem from './TodoItem'

export default {
  components: {
    TodoCreator, TodoItem
  },
  data () {
    return {
      db: null,
      // todos를 db로부터 불러와서 담기위한 배열선언
      todos: []
    }
  },
  created () {
    this.initDB()
  },
  methods: {
    // db 초기화만 진행 -> 목록존재하면 목록가져오기, 아니면 초기화진행
    initDB () {
      const adapter = new LocalStorage('todo-app')
      this.db = lowdb(adapter)
      // defaults는 lodash에서 제공
      console.log(this.db)
      // db 에 todos 가 존재하는지 확인함, 값을뽑아내려면 value붙임
      const hasTodos = _cloneDeep(this.db.has('todos').value())
      // 기존 데이터가 존재하면 가져오고, 없으면 초기화 시킴
      if (hasTodos) {
        this.todos = this.db.getState().todos
      } else {
        this.db
          .defaults({
            todos: [] // Collection
          }).write()
      }
    },
    createTodo (title) {
      // 받아야할 데이터 정의

      const newTodo = {
        id: cryptoRandomString({length: 10}),
        // title은 사용자가 입력한 값을 넣어줘야함
        // 이름 같으면 생략가능
        title,
        createdAt: new Date(),
        updatedAt: new Date(),
        done: false
      }

      this.db
        .get('todos') // lodash
        .push(newTodo) // lodash
        .write() // lowdb

      this.todos.push(newTodo)
    },
    updateTodo (todo, value) {
      this.db
        .get('todos')
        .find({id: todo.id})
        .assign(value)
        .write()

      const foundTodo = _find(this.todos, {id: todo.id})
      _assign(foundTodo, value)
    },
    deleteTodo () {
      console.log('deleteTodo')
    }
  }
}
</script>

<style>

</style>
