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
            :action="card.actions"
            :img="card.img"
            :players="players"
            :key="index"
            :position="index"
            :index="card.index"
            :currentStep="currentStep"
            :watchAllCards="watchAllCards"
            :active="card.active"
            :centered="card.centered"
            :cardIndex="card.index"
            :wolfsArr="wolfsArr"
            :centerCards="centerCards"
            :blocked="card.blocked"
            @makeonecentercardsblocked="onMakeOneCenterCardBlocked(index, $event)"
            @makeallcentercardsblocked="onMakeAllCenterCardsBlocked(index, $event)"
            @makeallplayerscardsblocked="onMakeAllPlayersCardsBlocked(index, $event)"
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
          :action="card.actions"
          :img="card.img"
          :players="players"
          :key="index"
          :position="index"
          :index="card.index"
          :currentStep="currentStep"
          :watchAllCards="watchAllCards"
          :active="card.active"
          :centered="card.centered"
          :cardIndex="card.index"
          :wolfsArr="wolfsArr"
          :playersArr="playersArr"
          :blocked="card.blocked"
          @changeblockflags="onChangeBlockFlags(index, $event)"
          @makeonecentercardsblocked="onMakeOneCenterCardBlocked(index, $event)"
          @makeallcentercardsblocked="onMakeAllCenterCardsBlocked(index, $event)"
          @makeallplayerscardsblocked="onMakeAllPlayersCardsBlocked(index, $event)"
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
        secondsForEachRole: 2000,
        // число активных игроков (у которых есть свой ход в игре)
        activeRoles: 7,
        watchAllCards: true,
        wolfsArr: [],
        cards: [
          {
            name: 'Werewolf',
            img: './img/werewolf.png',
            blocked: true,
            index: 0
          },
          {
            name: 'Werewolf',
            img: './img/werewolf.png',
            blocked: true,
            index: 1
          },
          {
            name: 'Minion',
            actions: [],
            img: './img/minion.png',
            blocked: true,
            index: 2
          },
          {
            name: 'Seer',
            img: './img/seer.png',
            blocked: true,
            index: 3
          },
          {
            name: 'Robber',
            img: './img/robber.png',
            blocked: true,
            index: 4
          },
          {
            name: 'Troublemaker',
            img: './img/troublemaker.png',
            blocked: true,
            index: 5
          },
          {
            name: 'Drunk',
            img: './img/drunk.png',
            blocked: true,
            index: 6
          },
          {
            name: 'Insomniac',
            img: './img/insomniac.png',
            blocked: true,
            index: 7
          },
          {
            name: 'Prince',
            img: './img/prince.png',
            blocked: true,
            index: 8
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

      stepWolves() {

        for (let i = 0; i < this.centerCards.length; i++) {
          this.centerCards[i].blocked = false;
        }

      },

      stepSeer() {

        for (let i = 0; i < this.cards.length; i++) {
          this.cards[i].blocked = false;
        }

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

        if (this.currentStep < 1) {
          this.stepWolves();
        }

        let step = data.index;

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
                if (self.constArrPlayers[i].index === 3) {
                  self.stepSeer();
                }
              }
              break;
            case 4:
              console.log('robber');
              break;
            case 5:
              console.log('troublemaker');
              break;
            case 6:
              console.log('drunk');
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
