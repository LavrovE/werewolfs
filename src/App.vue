<template>
  <div>
    <div class="container-fluid">
      <div class="first-step text-center bg-danger"
           @click="setOptions"
           v-if="opening"
      >
        <h1>Старт</h1>
      </div>
      <div class="cards"
           v-if="game"
      >
        <div class="flex-center">
          <app-card
            v-for="(card, index) in  centerCards"
            :name="card.name"
            :img="card.img"
            :players="players"
            :key="index"
            :position="index"
            :constIndex="card.constIndex"
            :currentStep="currentStep"
            :watchAllCards="watchAllCards"
            :active="card.active"
            :centered="card.centered"
            :cardIndex="card.index"
            :wolfsArr="wolfsArr"
            :constArrPlayers="constArrPlayers"
            :centerCards="centerCards"
            :blocked="card.blocked"
            :flipped="card.flipped"
            :picked="card.picked"
            @makeonecentercardsblocked="onMakeOneCenterCardBlocked(index, $event)"
            @makeallcentercardsblocked="onMakeAllCenterCardsBlocked(index, $event)"
            @makeallplayerscardsblocked="onMakeAllPlayersCardsBlocked(index, $event)"
            @showcardrobber="onShowCardRobber(index,$event)"
            @swapcardrobber="onSwapCardRobber(index, $event)"
            @pickcardtroublemaker="onPickCardTroubleMaker(index, $event)"
            @swapcardstroublemaker="onSwapCardsTroubleMaker(index, $event)"
            @makeallcardsunpicked="onMakeAllCardsUnpicked(index, $event)"
            @swapcarddrunk="onSwapCardDrunk(index, $event)"
            @changedata="onChangeData(index, $event)"
            :finalButton="finalButton"
            :currentRoleCard="card.currentRoleCard"
          >
          </app-card>
        </div>
      </div>
      <transition name="boom" mode="out-in">
        <div class="current-role-wrapper" v-if="showRoleInstruction">
          <h2>Текущий ход: {{ roleNameFromIndex }}</h2>
          <h1 class="current-role"
              :style="currentBackground"
          >
          </h1>
        </div>
      </transition>
      <div v-if="endGame" class="final-button-wrapper">
        <h1 class="final-button"
            @click="showAllCards"
        >
          Показать все карты
        </h1>
      </div>
      <div class="flex-flop"
           v-if="checkRoles || game"
      >
        <app-card
          v-for="(card, index) in  playersArr"
          :name="card.name"
          :img="card.img"
          :players="players"
          :key="index"
          :position="index"
          :constIndex="card.constIndex"
          :currentStep="currentStep"
          :watchAllCards="watchAllCards"
          :active="card.active"
          :centered="card.centered"
          :centerCards="centerCards"
          :cardIndex="card.index"
          :wolfsArr="wolfsArr"
          :playersArr="playersArr"
          :constArrPlayers="constArrPlayers"
          :blocked="card.blocked"
          :flipped="card.flipped"
          :picked="card.picked"
          @changeblockflags="onChangeBlockFlags(index, $event)"
          @makeonecentercardsblocked="onMakeOneCenterCardBlocked(index, $event)"
          @makeallcentercardsblocked="onMakeAllCenterCardsBlocked(index, $event)"
          @makeallplayerscardsblocked="onMakeAllPlayersCardsBlocked(index, $event)"
          @showcardrobber="onShowCardRobber(index,$event)"
          @swapcardrobber="onSwapCardRobber(index, $event)"
          @pickcardtroublemaker="onPickCardTroubleMaker(index, $event)"
          @swapcardstroublemaker="onSwapCardsTroubleMaker(index, $event)"
          @makeallcardsunpicked="onMakeAllCardsUnpicked(index, $event)"
          @swapcarddrunk="onSwapCardDrunk(index, $event)"
          @changedata="onChangeData(index, $event)"
          :finalButton="finalButton"
          :currentRoleCard="card.currentRoleCard"
        >
        </app-card>
      </div>
      <app-button
        v-if="checkRoles"
        :currentStep="roleNameFromIndex"
        :watchAllCards="watchAllCards"
        @changefirstrole="onChangeFirstRole($event)"
      >

      </app-button>
    </div>
  </div>
</template>

<script>

  import AppCard from './components/Card';
  import AppButton from './components/Button';


  export default {
    data() {
      return {
        //Этап 1 - нажатие кнопки и начало смешивания карт
        opening: true,
        //Этап 2 - Проверка ролей
        checkRoles: false,
        //Этап 3 - Старт игры с центральными картами и картами игроков (смотреть ВСЕМ на данном этапе уже нельзя)
        game: false,
        //Имена и колиество игроков
        players: ['Рома', 'Вова', 'Аня', 'Женя', 'Таня', 'Родик'],
        // Карты игроков после шафла
        playersArr: [],
        // Центральные карты
        centerCards: [],
        // Карты игроков, которые были сданы изначально (независимо от обмена картами)
        constArrPlayers: [],
        // счетчик ходов
        currentStep: 0,
        // таймер для каждого хода (в миллисекундах)
        secondsForEachRole: 10000,
        // число активных игроков (у которых есть свой ход в игре)
        activeRoles: 7,
        // флаг для анимации подсказки текущей роли
        showRoleInstruction: false,
        finalButton:false,
        endGame:false,
        watchAllCards: true,
        wolfsArr: [],
        cards: [
          {
            name: 'Werewolf',
            img: '/src/img/werewolf.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 0
          },
          {
            name: 'Werewolf',
            img: '/src/img/werewolf.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 1
          },
          {
            name: 'Minion',
            actions: [],
            img: '/src/img/minion.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 2
          },
          {
            name: 'Seer',
            img: '/src/img/seer.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 3
          },
          {
            name: 'Robber',
            img: '/src/img/robber.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 4
          },
          {
            name: 'Troublemaker',
            img: '/src/img/troublemaker.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 5
          },
          {
            name: 'Drunk',
            img: '/src/img/drunk.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 6
          },
          {
            name: 'Insomniac',
            img: '/src/img/insomniac.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 7
          },
          {
            name: 'Prince',
            img: '/src/img/prince.png',
            blocked: true,
            flipped: false,
            picked: false,
            currentRoleCard: false,
            constIndex: 9
          }
        ]
      }
    },
    created() {
      for (let i = 0; i < this.cards.length; i++) {
        this.cards[i].active = false;
        this.cards[i].centered = false;
      }
    },
    computed: {
      roleNameFromIndex() {
        return this.cards[this.currentStep].name;
      },
      currentBackground() {
        return 'background-image:url(' + this.cards[this.currentStep].img + ')';
      }
    },
    watch: {
      currentStep: function () {

        // Добавляем классы, роли , варианты ходов в зависимости от текущего

        switch (this.currentStep) {
          case 2:
            break;
          case 3:
            for (let i = 0; i < this.constArrPlayers.length; i++) {
              if (this.constArrPlayers[i].constIndex === 3) {
                this.stepSeer();
                this.constArrPlayers[i].currentRoleCard = true;
              }
            }
            break;
          case 4:
            for (let i = 0; i < this.constArrPlayers.length; i++) {
              if (this.constArrPlayers[i].constIndex === 4) {
                this.stepRobber();
                this.constArrPlayers[i].currentRoleCard = true;
              }
            }
            break;
          case 5:
            for (let i = 0; i < this.constArrPlayers.length; i++) {
              if (this.constArrPlayers[i].constIndex === 5) {
                this.stepTroublemaker();
                this.constArrPlayers[i].currentRoleCard = true;
              }
            }
            break;
          case 6:
            for (let i = 0; i < this.constArrPlayers.length; i++) {
              if (this.constArrPlayers[i].constIndex === 6) {
                this.unblockCenterCards();
                this.constArrPlayers[i].currentRoleCard = true;
              }
            }
            break;
          case 7:
            for (let i = 0; i < this.constArrPlayers.length; i++) {
              if (this.constArrPlayers[i].constIndex === 7) {
                this.playersArr[i].currentRoleCard = true;
                this.playersArr[i].blocked = false;
              }
            }
            break;
        }
      }
    },
    methods: {
      centerDisabled(index, data) {
        this.showCenter = data.showCenter;
      },

      refreshAllClassesCards() {
        this.blockAllCards();
        this.refreshCenterAndActiveCards();
        this.unflipAllCards();
        this.unpickAllCards();

      },
      shuffleCards() {
        for (let i = 0; i < this.players.length; i++) {
          while (1) {
            const card = this.cards[Math.floor(Math.random() * (this.cards.length))];
            if (!card.active) {
              card.active = true;
              this.playersArr.push(card);
              this.constArrPlayers.push(card);
              break;
            }
          }
        }
        for (let y = 0; y < this.cards.length; y++) {
          if (!this.cards[y].active) {
            this.cards[y].centered = true;
            this.centerCards.push(this.cards[y]);
          }
        }
        this.checkForWolfes();
      },

      unblockAllCards() {
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].blocked = false;
        }
      },

      unblockCenterCards() {
        for (let i = 0; i < this.centerCards.length; i++) {
          this.centerCards[i].blocked = false;
        }
      },

      unblockPlayerCards() {
        for (let i = 0; i < this.playersArr.length; i++) {
          this.playersArr[i].blocked = false;
        }
      },

      removeActiveRoleCard(){

        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].currentRoleCard = false;
        }

      },

      blockAllCards() {
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].blocked = true;
        }
      },

      unpickAllCards() {
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].picked = false;
        }
      },

      unflipAllCards() {
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].flipped = false;
        }
      },

      refreshCenterAndActiveCards() {
        for (let i = 0; i < this.centerCards.length; i++) {
          this.centerCards[i].centered = true;
          this.centerCards[i].active = false;

        }
        for (let i = 0; i < this.playersArr.length; i++) {
          this.playersArr[i].active = true;
          this.playersArr[i].centered = false;
        }
      },

      stepWolves() {
        this.unblockCenterCards();
      },

      stepSeer() {
        this.unblockAllCards();
      },

      stepRobber() {
        this.unblockPlayerCards();
      },

      stepTroublemaker() {
        this.unblockPlayerCards();
      },

      checkForWolfes() {
        let self = this;
        for (let i = 0; i < self.constArrPlayers.length; i++) {
          if (self.constArrPlayers[i].name == 'Werewolf') {
            self.wolfsArr.push(self.constArrPlayers[i]);
          }
        }
      },

      setOptions() {
        this.shuffleCards();
        this.opening = false;
        this.checkRoles = true;
      },
      onChangeData(index, data) {
        this.cards[index].name = data.name;
      },

      onChangeFirstRole(data) {

        this.checkRoles = false;

        this.game = true;

        this.watchAllCards = false;

        let step = data.firstRole;


        this.currentStep = step;

        this.showRoleInstruction = true;

        if (this.wolfsArr.length === 1) {
          this.stepWolves();
        }

        let self = this;

        let timerId = setInterval(function () {

          self.blockAllCards();

          self.removeActiveRoleCard();

          self.currentStep++;

          self.refreshAllClassesCards();

          if (self.currentStep > self.activeRoles) {
            clearInterval(timerId);
            self.blockAllCards();
            self.showRoleInstruction = false;
            self.endGame = true;
          }

        }, this.secondsForEachRole);
      },
      onMakeAllCenterCardsBlocked(index, data) {
        for (let i = 0; i < this.centerCards.length; i++) {
          this.centerCards[i].blocked = true;
        }
      },

      onMakeAllPlayersCardsBlocked(index, data) {
        for (let i = 0; i < this.playersArr.length; i++) {
          this.playersArr[i].blocked = true;
        }
      },

      onMakeOneCenterCardBlocked(index, data) {
        this.centerCards[index].blocked = true;
      },

      onMakeAllCardsUnpicked(index, data) {
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].picked = false;
        }
      },

      onShowCardRobber(index, data) {

        this.playersArr[data.position].flipped = true;

        for (let i = 0; i < this.playersArr.length; i++) {
          this.playersArr[i].blocked = true;
        }
        this.playersArr[data.position].blocked = false;

      },

      onSwapCardRobber(index, data) {

        // карта, которую кликнули

        let temp = this.playersArr[data.position];

        // дефолтный индекс роббера

        let robberCardIndex;

        for (let i = 0; i < this.playersArr.length; i++) {
          if (this.playersArr[i].constIndex === 4) {
            robberCardIndex = i;
          }
        }

        this.$set(this.playersArr, data.position, this.playersArr[robberCardIndex]);
        this.$set(this.playersArr, robberCardIndex, temp);
        this.blockAllCards();
        this.unflipAllCards();
      },

      onPickCardTroubleMaker(index, data) {

        this.playersArr[data.position].picked = true;

      },

      onSwapCardsTroubleMaker(index, data) {

        let temp = this.playersArr[data.position];

        let pickedCardIndex;

        for (let i = 0; i < this.playersArr.length; i++) {
          if (this.playersArr[i].picked) {
            pickedCardIndex = i;
          }
        }
        this.$set(this.playersArr, data.position, this.playersArr[pickedCardIndex]);
        this.$set(this.playersArr, pickedCardIndex, temp);
        this.blockAllCards();
      },

      onSwapCardDrunk(index, data) {
        let clickedCard = this.centerCards[data.position];
        let constDrunkCardIndex;

        for (let i = 0; i < this.constArrPlayers.length; i++) {
          if (this.constArrPlayers[i].constIndex === 6) {
            constDrunkCardIndex = i;
          }
        }

        this.$set(this.centerCards, data.position, this.playersArr[constDrunkCardIndex]);
        this.$set(this.playersArr, constDrunkCardIndex, clickedCard);

        this.refreshCenterAndActiveCards();
        this.blockAllCards();
      },

      showAllCards(){
        this.unblockAllCards();
        this.finalButton = true;
      }

    },
    components: {
      AppCard,
      AppButton
    }
  }
</script>
<style>
  html {
    font-size: 0.05208vw;
    overflow: hidden;
  }

  .flex-center {
    display: flex;
    justify-content: center;
  }

  .flex-flop {
    display: flex;
    justify-content: space-between;
  }

  body {
    background-image: url(/src/img/bg.jpg);
    background-size: cover;
    background-repeat: no-repeat;
    max-width: 1920rem;
    margin: 0 auto;
    overflow: hidden;
    transition: 0.2s;
  }

  body * {
    transition: 0.2s;
  }

  .container-fluid {
    padding-right: 15rem;
    padding-left: 15rem;
    margin-right: auto;
    margin-left: auto;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .first-step {
    max-width: 400rem;
    display: block;
    margin: 0 auto;
    padding: 0 90rem;
    background-color: #4dff28;
    border-radius: 10rem;
    cursor: pointer;
  }

  .first-step h1 {
    line-height: 109rem;
  }

  .first-step:hover {
    background-color: #d58512;
  }

  .current-role-wrapper {
    display: flex;
    width: 100%;
    text-align: center;
    align-items: center;
    justify-content: center;
    background-repeat: no-repeat;

  }

  .current-role-wrapper h2 {
    color: #e5fa04;
    margin-right: 20rem;
    background: #e5fa043b;
    padding: 20rem;
    border-radius: 12rem;
    border: 2rem solid #e5fa04;
  }

  .current-role {
    background-repeat: no-repeat;
    display: block;
    height: 100rem;
    background-size: cover;
    width: 100rem;
    border-radius: 200rem;
    border: 2rem solid #e5fa04;
  }

  .boom-enter-active {
    animation: slideIn 0.5s;
  }

  .boom-enter-to {
    animation: slideIn 0.5s;
  }

  .boom-leave {
    animation: slideOut 0.5s;
  }

  .boom-leave-active {
    animation: slideOut 0.5s;
  }

  .final-button-wrapper{
    display: flex;
    width: 100%;
    align-items: center;
    justify-content: center;
    text-align: center;
  }

  .final-button{
    padding: 20rem 20rem;
    -webkit-border-radius: 14px;
    -moz-border-radius: 14px;
    border-radius: 14px;
    color: #000000;
    background: #e5fa04;
  }

  @keyframes slideIn {
    from {
      transform: translateX(100vw);
    }
    to {
      transform: translateX(0px);
    }
  }

  @keyframes slideOut {
    from {
      transform: translateX(0px);
    }
    to {
      transform: translateX(-100vw);
    }
  }

  @media (min-width: 992px) and (min-device-width: 992px) {

  }

</style>
