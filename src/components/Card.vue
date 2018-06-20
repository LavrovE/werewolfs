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
      <p>card index = {{constIndex}}</p>
      <p>flipped = {{flipped}}</p>
      <p>picked = {{picked}}</p>
    </div>
  </div>
</template>

<script>
  export default {
    props: [
      'name',
      'img',
      'players',
      'position',
      'constIndex',
      'currentStep',
      'watchAllCards',
      'centered',
      'active',
      'cardIndex',
      'wolfsArr',
      'playersArr',
      'constArrPlayers',
      'centerCards',
      'blocked',
      'flipped',
      'picked'
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
      watchCardNoHide() {
        if (!this.blocked || this.watchAllCards) {
          this.showCard = true;
        }
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
            case 1:
              if (this.wolfsArr.length === 1) {
                if (this.centered && !this.blocked) {
                  this.watchCard();
                  this.makeAllCenterCardsBlocked(e, index);
                }
              }
              break;
            // seer
            case 3:

              for (let i = 0; i < this.constArrPlayers.length; i++) {

                if (this.constArrPlayers[i].constIndex === 3) {

                  if (this.constIndex !== 3 && this.centered) {

                    this.makeAllPlayersCardsBlocked(e, index);

                    this.watchCard();

                    this.makeOneCenterCardBlocked(e, index);

                    let cardsArr = [];

                    for (let i = 0; i < this.centerCards.length; i++) {
                      if (this.centerCards[i].blocked) {
                        cardsArr.push(this.centerCards[i]);
                      }
                    }
                    if (cardsArr.length > 1) {
                      this.makeAllCenterCardsBlocked(e, index);
                    }
                  }
                  else if (this.constIndex !== 3 && this.active) {

                    let cardsArr = [];

                    for (let i = 0; i < this.centerCards.length; i++) {
                      if (this.centerCards[i].blocked) {
                        cardsArr.push(this.centerCards[i]);
                      }
                    }

                    // если нажали на одну центральную карту, а потом на игровую

                    if (cardsArr.length <= 0) {
                      this.watchCard();
                      this.makeAllCenterCardsBlocked(e, index);
                      this.makeAllPlayersCardsBlocked(e, index);
                    } else {

                    }
                  }

                }
              }
              break;
            // robber
            case 4:
              for (let i = 0; i < this.constArrPlayers.length; i++) {
                if (this.constArrPlayers[i].constIndex === 4) {
                  if (this.constIndex !== 4 && this.active) {
                    this.makeAllPlayersCardsBlocked(e, index);
                    if (this.flipped) {
                      this.swapCardRobber(e, index);
                      this.hideCard();
                    } else {
                      this.showCardRobber(e, index);
                      this.watchCardNoHide();
                    }
                  }
                }
              }
              break;
            // troublemaker
            case 5:
              for (let i = 0; i < this.constArrPlayers.length; i++) {

                if (this.constArrPlayers[i].constIndex === 5) {

                  if (this.constIndex !== 5 && this.active && !this.blocked) {

                    // первый клик

                    let tempArr = [];

                    for (let y = 0; y < this.playersArr.length; y++) {
                      if (this.playersArr[y].picked) {
                        tempArr.push(this.playersArr[y].picked);
                        this.swapCardTroubleMaker(e, index);
                        this.makeAllCardsUnpicked(e, index);
                      }
                    }
                    if (tempArr.length === 0) {
                      this.pickCardTroubleMaker(e, index);
                      this.makeAllCenterCardsBlocked(e, index);
                      this.makeAllPlayersCardsBlocked(e, index);
                    }
                  }
                }
              }
              break;
            // drunk
            case 6:
              for (let i = 0; i < this.constArrPlayers.length; i++) {
                if (this.constArrPlayers[i].constIndex === 6) {
                  if (this.centered && !this.blocked) {
                    this.swapCardDrunk(e, index);
                  }
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

      makeAllCardsUnpicked(e, index) {
        this.$emit('makeallcardsunpicked', {});
      },


      showCardRobber(e, index) {
        this.$emit('showcardrobber', {
          position: this.position
        });
      },

      swapCardRobber(e, index) {
        this.$emit('swapcardrobber', {
          position: this.position
        });
      },

      pickCardTroubleMaker(e, index) {
        this.$emit('pickcardtroublemaker', {
          position: this.position
        });
      },

      swapCardTroubleMaker(e, index) {
        this.$emit('swapcardstroublemaker', {
          position: this.position
        });
      },

      swapCardDrunk(e, index) {
        this.$emit('swapcarddrunk', {
          position: this.position
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
