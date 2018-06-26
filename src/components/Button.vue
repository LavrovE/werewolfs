<template>
  <div>
    <div class="start text-center"
         @click="startGame"
    >
      <h1>Запуск сценария</h1>
    </div>
  </div>
</template>

<script>
  export default {
    props: ['currentStep'],
    data() {
      return {
        firstRole: 1
      }
    },
    computed: {},
    methods: {
      startGame(e) {
        this.$emit('changefirstrole', {
          firstRole: this.firstRole
        });
      }
      // onClick(e) {
      //   this.$emit('changearr', {
      //     newName : this.newName
      //   })
      // }
    }
  }
</script>

<style scoped>
  .start {
    display: block;
    margin: 0 auto;
    padding: 20rem 30rem;
    background-color: #4dff28;
    border-radius: 10rem;
    cursor: pointer;
  }
  .start:hover{
    background-color: #d58512;
  }
  .start h1 {
  }
</style>
