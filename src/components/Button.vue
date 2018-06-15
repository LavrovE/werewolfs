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
        firstRole: 0
      }
    },
    computed: {},
    methods: {
      startGame(e) {
        let count = 0;
        count ++;
        this.$emit('changefirstrole', {
          firstRole: this.firstRole,
          index: count
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
