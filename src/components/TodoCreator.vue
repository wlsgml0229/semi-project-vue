<template>
   <div>
       <button @click="createTodo">추가</button>
       <input
         :value="title"
         :placeholder="placeholder"
         @input="title= $event.target.value"
         @keypress.enter="createTodo"
        type ="text"/>
   </div>
</template>

<script>
export default{
  data () {
    return {
      title: '',
      // 데이터를 직접 html코드에 적는거 보다 바인딩시키는게 좋음
      placeholder: '할 일을 추가하세요'
    }
  },
  methods: {
    createTodo () {
    // 유효성 검사
      const validatedTitle = this.title && this.title.trim()
      // validatedTitle이 false일때 경고
      if (!validatedTitle) {
        alert('유효하지 않은 제목입니다!')
        this.title = this.title.trim()
        return
      }
      // 부모컴포넌트에게 이벤트를 올려주고, title전달
      this.$emit('create-todo', this.title)
      // title을 초기화 해줌
      this.title = ''
    }
  }
}
</script>
