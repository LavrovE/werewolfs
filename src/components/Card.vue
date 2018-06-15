<template>
  <div>
    watch all = {{watchAllCards}}
    <div class="card"
         @click="onClick"
    >
      <h3 v-if="showCard">
        {{ name }}
      </h3>
      <h3 v-else-if="!showCard && active">
        {{players[index]}}
      </h3>
      <p>card index = {{cardIndex}}</p>
    </div>
  </div>
</template>

<script>
  export default {
    props: ['name', 'action', 'img', 'players', 'index', 'currentStep', 'watchAllCards', 'centered', 'active', 'cardIndex', 'wolfsArr', 'playersArr', 'centerCards', 'watched'],
    data() {
      return {
        showCard: false
      }
    },
    computed: {},
    methods: {
      watchCard() {
        this.showCard = true;
        setTimeout(this.hideCard, 2000);
      },
      hideCard() {
        this.showCard = false;
      },
      selfRoleFunction(e, index) {
        switch (this.watchAllCards) {
          case true:
            this.watchCard();
            break;
        }
        if (!this.watchAllCards) {
          switch (this.currentStep) {
            case 'watch-roles':
              break;
            case 'Werewolf':
              if (this.wolfsArr.length === 1) {
                if (this.centered && !this.watched) {
                  this.watchCard();
                  this.watched = true;
                }
              }
              break;
            case 'Minion':
              break;
            case 'Seer':
              // this.watchCard();
              break;
            case 'Robber':
              // this.watchAndSwapNoCenterCard();
              break;
            case 'Troublemaker':
              // this.swapOtherNotCenterCards();
              break;
            case 'Drunk':
              // this.swapCenterCard();
              break;
            case 'Insomniac':
              // this.watchCard();
              break;
            case 'Prince':
              // alert('Мимо');
              break;
          }
        }
      }
      ,
      onClick(e, index) {
        this.selfRoleFunction(e, index);
      }
    }
  }
</script>

<style scoped>

  .card {
    display: block;
    background: red;
    width: 150px;
    height: 150px;
    margin: 0px 20px;
  }
</style>
