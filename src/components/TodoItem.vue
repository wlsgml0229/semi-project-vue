<template>
    <div class="todo-item">
        <div
         v-if="isEditMode"
        class="item_inner item--edit">
        <input
        ref="titleInput"
        :value="editedTitle"
         type="text"
         @input="editedTitle = $event.target.value"
         @keypress.enter="editedTodo"
         @keypress.esc="offEditMode"
         />
            <div class = "item_actions">
            <button
            key="complete"
             @click="editedTodo">완료</button>
            <button
             key="cancel"
             @click="offEditMode">취소</button>
            </div>
        </div>
    <div v-else
        class="item_inner item--normal">
         <input
         v-model="done"
         type="checkbox"/>
        <div class = "item_title-wrap">
        <div class="item_title">
            {{todo.title}}
        </div>
        <div class="item_date">
            {{todo.createdAt}}
        </div>
      </div>
        <div class="item_actions">
            <button @click="onEditMode">수정</button>
            <button @click="deleteTodo">삭제</button>
        </div>
    </div>

    </div>
</template>

<script>
import dayjs from 'dayjs'

export default {
  props: {
    todo: Object
  },
  data () {
    return {
      // 일반적인 경우 수정모드가 아니니까 flase로 시작
      isEditMode: false,
      editedTitle: ''
    }
  },
  computed: {
    done: {
      get () {
        return this.todo.done
      },
      // 변경된 값 매개변수 등록
      set (done) {
        this.updateTodo({
          // 속성이름과 값이 같으면 생략가능
          done: done
        })
      }
    },
    date () {
      const date = dayjs(this.todo.createdAt)
      const isSame = date.isSame(this.todo.updatedAt)
      if (isSame) {
        return date.format('YYYY년 MM월 DD일')
      } else {
        return `${date.fromat('YYYY년 MM월 DD일')} (edited)`
      }
    }
  },
  methods: {
    editedTodo () {
      // 수정 한 경우만 로직 실행
      if (this.todo.title !== this.editedTitle) {
        this.updateTodo({
          title: this.editedTitle,
          updatedAt: new Date()
        })
      }
      // EDIT 모드 수정완료
      this.offEditMode()
    },
    onEditMode () {
      this.isEditMode = true
      this.editedTitle = this.todo.title // 원래값 돌아갈수 있도록 로직 생성해줌
      // 해당하는 인풋요소를 포커스 해달라는 뜻

      // Vue.js가 데이터 변경 후 DOM 업데이트를 마칠 때까지 기다림.
      this.$nextTick(() => {
        this.$refs.titleInput.focus()
      })
    },
    offEditMode () {
      this.isEditMode = false
    },
    updateTodo (value) {
      // 부모컴포넌트로 넘겨주는 역할
      // 어떤 todo인지 알려줘야하니까 this.todo 추가
      this.$emit('update-todo', this.todo, value)
    },
    deleteTodo () {
      this.$emit('delete-todo', this.todo)
    }
  }
}
</script>
