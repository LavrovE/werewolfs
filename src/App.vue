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
          >
          </app-card>
        </div>
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
      <p>current role - {{roleNameFromIndex}}</p>
      <p>current role id - {{currentStep}}</p>
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
        watchAllCards: true,
        wolfsArr: [],
        cards: [
          {
            name: 'Werewolf',
            img: './img/werewolf.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 0
          },
          {
            name: 'Werewolf',
            img: './img/werewolf.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 1
          },
          {
            name: 'Minion',
            actions: [],
            img: './img/minion.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 2
          },
          {
            name: 'Seer',
            img: './img/seer.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 3
          },
          {
            name: 'Robber',
            img: './img/robber.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 4
          },
          {
            name: 'Troublemaker',
            img: './img/troublemaker.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 5
          },
          {
            name: 'Drunk',
            img: './img/drunk.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 6
          },
          {
            name: 'Insomniac',
            img: './img/insomniac.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 7
          },
          {
            name: 'Prince',
            img: './img/prince.png',
            blocked: true,
            flipped: false,
            picked: false,
            constIndex: 8
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
      }
    },
    methods: {
      centerDisabled(index, data) {
        this.showCenter = data.showCenter;
        console.log(this.showCenter)
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
          console.log('unblocked')
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

      blockAllCards() {
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].blocked = true;
        }
      },

      refreshCenterAndActiveCards(){
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

      stepTroublemaker(){
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

        if (this.currentStep < 2) {
          this.stepWolves();
        }



        let self = this;

        let timerId = setInterval(function () {

          self.currentStep++;

          // Добавляем классы, роли , варианты ходов в зависимости от текущего

          switch (self.currentStep) {
            case 2:
              console.log('minion');
              break;
            case 3:
              console.log('seer');
              for (let i = 0; i < self.constArrPlayers.length; i++) {
                if (self.constArrPlayers[i].constIndex === 3) {
                  self.stepSeer();
                }
              }
              break;
            case 4:
              console.log('robber');
              for (let i = 0; i < self.constArrPlayers.length; i++) {
                if (self.constArrPlayers[i].constIndex === 4) {
                  self.stepRobber();
                } else {
                }
              }
              break;
            case 5:
              for (let i = 0; i < self.constArrPlayers.length; i++) {
                if (self.constArrPlayers[i].constIndex === 5) {
                  self.stepTroublemaker();
                }
              }
              break;
            case 6:
              for (let i = 0; i < self.constArrPlayers.length; i++) {
                if (self.constArrPlayers[i].constIndex === 6) {
                  self.unblockCenterCards();
                }
              }
              break;
            case 7:
              console.log('insomniac');
              break;
          }
          if (self.currentStep > self.activeRoles) {
            clearInterval(timerId);
            console.log('end of game')
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

      onMakeAllCardsUnpicked(index, data){
        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].picked = false;
        }
      },

      onShowCardRobber(index, data) {
          console.log(data);
          this.playersArr[data.position].flipped = true;
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
      },

      onPickCardTroubleMaker(index, data){

        this.playersArr[data.position].picked = true;

      },

      onSwapCardsTroubleMaker(index, data){

        let temp = this.playersArr[data.position];

        let pickedCardIndex;

        for(let i=0;i<this.playersArr.length;i++){
          if(this.playersArr[i].picked){
            pickedCardIndex = i;
          }
        }
        this.$set(this.playersArr, data.position, this.playersArr[pickedCardIndex]);
        this.$set(this.playersArr, pickedCardIndex, temp);
        this.blockAllCards();
      },

      onSwapCardDrunk(index, data){
        let clickedCard = this.centerCards[data.position];
        let drunkCardIndex;

        for(let i=0;i<this.constArrPlayers.length;i++){
          if(this.constArrPlayers[i].constIndex === 6){
            drunkCardIndex = i;
          }
        }
        this.$set(this.centerCards, data.position, this.constArrPlayers[drunkCardIndex]);
        this.$set(this.playersArr, drunkCardIndex, clickedCard);

        this.refreshCenterAndActiveCards();
        this.blockAllCards();
      }

    },
    components: {
      AppCard,
      AppButton
    }
  }
</script>
<style>
  .flex-center {
    display: flex;
    justify-content: center;
  }

  .flex-flop {
    display: flex;
    justify-content: space-between;
  }
</style>
