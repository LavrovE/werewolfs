<template>
  <div>
    <div class="button-start"
         @click="startGame"
    >
      Запуск сценария
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

</style>
