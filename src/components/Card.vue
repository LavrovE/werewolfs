<template>
  <div>
    <div class="card"
         :class="{
          'picked': picked,
         'blocked' :blocked ,
         'unblocked' :!blocked,
         'watchcards' : watchAllCards,
         'flipped': flipped,
         'currentCard': currentRoleCard && !watchAllCards && !centered && constIndex>2,
          'final':finalButton
          }"
         @click="onClick"
    >
      <h3 v-if="showCard || finalButton"
      >
        {{ name }}
      </h3>
      <h3 v-else-if="!showCard && active">
        {{players[position]}}
      </h3>
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
      'picked',
      'finalButton',
      'currentRoleCard'
    ],
    data() {
      return {
        showCard: false
      }
    },
    computed: {

    },
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
      singleWolf(e, index) {
        if (this.wolfsArr.length === 1) {
          if (this.centered && !this.blocked) {
            this.watchCard();
            this.makeAllCenterCardsBlocked(e, index);
          }
        }
      },
      seerRole(e, index) {

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
      },
      robberRole(e, index) {
        for (let i = 0; i < this.constArrPlayers.length; i++) {
          if (this.constArrPlayers[i].constIndex === 4) {
            if (this.constIndex !== 4 && this.active && !this.blocked) {
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
      },
      troublemakerRole(e, index) {
        for (let i = 0; i < this.constArrPlayers.length; i++) {
          if (this.constArrPlayers[i].constIndex === 5) {
            if (this.constIndex !== 5 && this.active && !this.blocked) {
              // первый клик
              let tempArr = [];
              if (!this.picked) {
                for (let y = 0; y < this.playersArr.length; y++) {
                  if (this.playersArr[y].picked) {
                    tempArr.push(this.playersArr[y].picked);
                    this.swapCardTroubleMaker(e, index);
                    this.makeAllCardsUnpicked(e, index);
                  }
                }
              }
              if (tempArr.length === 0) {
                this.pickCardTroubleMaker(e, index);
              }
            }
          }
        }
      },
      drunkRole(e, index) {
        for (let i = 0; i < this.constArrPlayers.length; i++) {
          if (this.constArrPlayers[i].constIndex === 6) {
            if (this.centered && !this.blocked) {
              this.swapCardDrunk(e, index);
            }
          }
        }
      },
      insomniacRole(e, index) {
        for (let i = 0; i < this.constArrPlayers.length; i++) {
          if (this.constArrPlayers[i].constIndex === 7) {
            if (!this.blocked && this.active) {
              this.watchCard();
              this.makeAllPlayersCardsBlocked(e, index);
            }
          }
        }
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
              this.singleWolf(e, index);
              break;
            // seer
            case 3:
              this.seerRole(e, index);
              break;
            // robber
            case 4:
              this.robberRole(e, index);
              break;
            // troublemaker
            case 5:
              this.troublemakerRole(e, index);
              break;
            // drunk
            case 6:
              this.drunkRole(e, index);
              break;
            // insomniac
            case 7:
              this.insomniacRole(e, index);
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

      checkCardInsomniac(e, index) {
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
    background: url("/src/img/shirt.jpeg");
    background-repeat: no-repeat;
    background-size: cover;
    height: 250rem;
    background-position: left center;
    margin: 10rem 16rem;
    padding: 20rem;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 10rem;
    min-width: 240rem;
    border: 3rem solid #e6fb07;
    cursor: pointer;
    transition: 0.2s;
  }

  .picked {
    border: 3rem solid #e6fb07;
    background-image: url("/src/img/picked.png");
    -webkit-box-shadow: 0px 0px 21px 11px rgba(238, 255, 0, 1);
    -moz-box-shadow: 0px 0px 21px 11px rgba(238, 255, 0, 1);
    box-shadow: 0px 0px 21px 11px rgba(238, 255, 0, 1);
  }

  .blocked {
    border: 3rem solid red;
    filter: blur(0px) grayscale(75%);
    box-shadow: none !important;
    -webkit-box-shadow: none !important;
    -moz-box-shadow: none !important;
  }

  .unblocked {
    border: 3rem solid #e6fb07;
    filter: none;
  }

  .flipped {
    border: 3rem solid #e6fb07 !important;
    filter: none !important;
  }

  .watchcards {
    border: 3rem solid #e6fb07;
    filter: none;
  }

  .final {
    background: #e6fb07;
    transition: 0.2s;
  }

  .currentCard {
    -webkit-box-shadow: 0px 0px 41px 17px rgba(238, 255, 0, 1);
    -moz-box-shadow: 0px 0px 41px 17px rgba(238, 255, 0, 1);
    box-shadow: 0px 0px 41px 17px rgba(238, 255, 0, 1);
  }

  .blocked.currentCard {
    -webkit-box-shadow: 0px 0px 41px 17px rgba(238, 255, 0, 1) !important;
    -moz-box-shadow: 0px 0px 41px 17px rgba(238, 255, 0, 1) !important;
    box-shadow: 0px 0px 41px 17px rgba(238, 255, 0, 1) !important;
  }

  .card h3 {
    font-size: 24rem;
    /*font-size: 0;*/
    background: #eaff00fa;
    padding: 12rem 8rem;
    border-radius: 4rem;
    color: #000000;
  }

  @media (min-width: 992px) and (min-device-width: 992px) {

    .card:hover {
      background: #e6fb07;
      transition: 0.2s;
    }

  }


</style>
