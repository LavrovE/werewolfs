<template>
  <div>
    watch all = {{watchAllCards}}
    <br>
    <span>
      blocked = {{ blocked }}
    </span>
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
    props: [
      'name',
      'action',
      'img',
      'players',
      'index',
      'currentStep',
      'watchAllCards',
      'centered',
      'active',
      'cardIndex',
      'wolfsArr',
      'playersArr',
      'centerCards',
      'blocked'
    ],
    data() {
      return {
        showCard: false
      }
    },
    computed: {},
    methods: {
      watchCard() {
        if (!this.blocked || this.watchAllCards) {
          this.showCard = true;
        }
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
                if (this.centered && !this.blocked) {
                  this.watchCard();
                  this.changeCenterBlockFlags(e,index);
                }
              }
              break;
            case 'Minion':
              break;
            case 'Seer':
              break;
            case 'Robber':
              break;
            case 'Troublemaker':
              break;
            case 'Drunk':
              break;
            case 'Insomniac':
              break;
            case 'Prince':
              break;
          }
        }
      },
      changeCenterBlockFlags(e, index) {
        this.$emit('changecenterblockflags', {
          index: this.index,
          blocked: this.blocked
      });
      },
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
