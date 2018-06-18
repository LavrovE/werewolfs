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
        {{players[position]}}
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
      'position',
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
            // werewolf
            case 0:
              if (this.wolfsArr.length === 1) {
                if (this.centered && !this.blocked) {
                  this.watchCard();
                  this.makeAllCenterCardsBlocked(e, index);
                }
              }
              break;
            // seer
            case 3:
              if (this.index !== 3) {
                if (this.centered) {

                  this.makeAllPlayersCardsBlocked();

                  this.watchCard();

                  this.makeOneCenterCardBlocked();

                  let cardsArr = [];

                  for (let i = 0; i < this.centerCards.length; i++) {
                    if (this.centerCards[i].blocked) {
                      cardsArr.push(this.centerCards[i]);
                    }
                  }
                  if (cardsArr.length > 1) {
                    this.makeAllCenterCardsBlocked();
                  }

                } else {
                  this.makeAllCenterCardsBlocked();

                  this.watchCard();

                  this.makeAllPlayersCardsBlocked();
                }
              }

              break;
          }
        }
      },
      makeAllCenterCardsBlocked(e, index) {
        this.$emit('makeallcentercardsblocked', {
          index: this.index,
          blocked: this.blocked
        });
      },
      makeAllPlayersCardsBlocked(e, index) {
        this.$emit('makeallplayerscardsblocked', {
          index: this.index,
          blocked: this.blocked
        });
      },
      makeOneCenterCardBlocked(e, index) {
        this.$emit('makeonecentercardsblocked', {
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
