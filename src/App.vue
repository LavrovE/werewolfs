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
            :index="index"
            :currentStep="roleNameFromIndex"
            :watchAllCards="watchAllCards"
            :active="card.active"
            :centered="card.centered"
            :cardIndex="card.index"
            :wolfsArr="wolfsArr"
            :centerCards="centerCards"
            :watched="card.watched"
            @watchonecenter="centerWasWatched(index, $event)"
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
          :index="index"
          :currentStep="roleNameFromIndex"
          :watchAllCards="watchAllCards"
          :active="card.active"
          :centered="card.centered"
          :cardIndex="card.index"
          :wolfsArr="wolfsArr"
          :playersArr="playersArr"
          :watched="card.watched"
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
    </div>
  </div>
</template>

<script>

  import AppCard from './components/Card';
  import AppButton from './components/Button';


  export default {
    data() {
      return {
        opening:true,
        checkRoles:false,
        game:false,
        players: ['Рома', 'Вова', 'Аня', 'Женя', 'Таня', 'Родик'],
        playersArr: [],
        centerCards: [],
        constArrPlayers: [],
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
            actions: ['watch'],
            img: './img/werewolf.png',
            index: 0
          },
          {
            name: 'Werewolf',
            actions: ['watch'],
            img: './img/werewolf.png',
            index: 1
          },
          {
            name: 'Minion',
            actions: [],
            img: './img/minion.png',
            index: 2
          },
          {
            name: 'Seer',
            actions: ['watch'],
            img: './img/seer.png',
            index: 3
          },
          {
            name: 'Robber',
            actions: ['watch-and-swap'],
            img: './img/robber.png',
            index: 4
          },
          {
            name: 'Troublemaker',
            actions: ['swap-two'],
            img: './img/troublemaker.png',
            index: 5
          },
          {
            name: 'Drunk',
            actions: ['swap-center'],
            img: './img/drunk.png',
            index: 6
          },
          {
            name: 'Insomniac',
            actions: ['watch'],
            img: './img/insomniac.png',
            index: 7
          },
          {
            name: 'Prince',
            actions: [],
            img: './img/prince.png',
            index: 8
          }
        ]
      }
    },
    created() {
      for (let i = 0; i < this.cards.length; i++) {
        this.cards[i].active = false;
        this.cards[i].centered = false;
        this.cards[i].watched = false;
      }
    },
    computed: {
      roleNameFromIndex() {
        return this.cards[this.currentStep].name;
      }
    },
    methods: {
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
      checkForWolfes() {
        let self = this;
        for (let i = 0; i < self.constArrPlayers.length; i++) {
          // console.log(self.constArrPlayers[i].name == "Werewolf")
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
      // centerWasWatched(index, data) {
      //   for (let i = 0; i < this.centerCards.length; i++) {
      //     // this.centerCards[i].watched = data.watched;
      //     if(data.watched){
      //       this.centerCards[i].watched = data.watched;
      //     }
      //   }
      // },

      onChangeFirstRole(data) {

        this.checkRoles = false;
        this.game = true;

        this.watchAllCards = false;

        this.currentStep = data.firstRole;

        let step = data.index;

        let self = this;

        let timerId = setInterval(function () {

          self.currentStep++;

          console.log(self.currentStep);

          if (self.currentStep > self.activeRoles) {
            clearInterval(timerId);
          }

        }, this.secondsForEachRole);



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
