<template>
  <q-page class="container">
    <!-- header menu -->
    <div class="fit">
      <q-btn color="cyan" label="menu" />
    </div>

    <div class="row no-wrap container__card ">
      <!-- star-dust -->
      <div class="q-mt-md col-4 bg-grey-2 starship pixel-borders--1">
        <div class="flex justify-center">
          <q-btn outline flat :label="game.starCompanyName" @click="starCompany"/>
        </div>
        <div class="column items-center q-mb-md">
          <div>
            Poeira Cosmica: {{ Math.round(cosmicDustCount)}}
          </div>
          <div>Por segundo: {{ game.cosmicDustPerSecond }}</div>
        </div>
        <div class="justify-center flex">
          <q-btn icon="ion-rocket" round  glossy @click="getDust()"/>
        </div>
        <div>
          upgrades instalados
        </div>
      </div>

      <!-- itens -->
      <div class="q-mt-md q-ml-md col-4 bg-grey-2 starship pixel-borders--1">
        <div class="flex justify-center">
        loja de itens
        </div>
        <!-- TODO loopar os items -->
        <q-list bordered separator class="starship__item-list text-white">
          <q-item v-for="(item, key) in game.items" :key="key" class="starship__items">
            <q-item-section>
              <q-btn label="comprar" push color="green" @click="buyItem(item)" />
              <div>{{ item.label }}</div>
              <div class="flex justify-between">
                <div>preço: {{ Math.round(item.price) }}</div>
                <div>adiciona: {{item.value}}/s</div>
              </div>
              <div class="flex justify-center">
                <img :src="require(`../assets/${item.img}`)">
              </div>
              <div class="q-px-md q-my-md starship__item-description">{{ item.description }}</div>
              <div>Compradas: {{ item.amount }} unidades</div>
            </q-item-section>
          </q-item>
        </q-list>
      </div>

      <!-- upgrades -->
      <div class="q-mt-md q-ml-md bg-grey-2 starship pixel-borders--1 ">
        <q-list bordered separator class="starship__item-list">
          <div class="flex justify-center">
              Upgrades
          </div>
          <q-item v-for="(item, key) in game.items" :key="key">
            <q-item-section v-if="game.avaliableUpdate.length">
              <div>{{ item.label }} </div>
              <q-list bordered separator class="starship__item-update">
                <q-item v-for="(upgrade, key) in game.avaliableUpdate" :key="key" clickable @click="buyUpgrade(upgrade)">
                  <q-item-section>
                    <div>{{ upgrade.label }} </div>
                    <div>{{upgrade.description}}</div>
                    <div>Preço: {{ upgrade.price }} </div>
                  </q-item-section>
                </q-item>
              </q-list>
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </div>

    <!-- company name dialog -->
    <q-dialog v-model="setName">
        <q-card style="width: 300px">
          <q-card-section>
            <q-icon name="ion-rocket" />
            <!-- TODO Resolver com watch a reatividade, so mudar quando salvar -->
            <q-input v-model="game.starCompanyName" label="Qual nome da sua companhia?" />
          </q-card-section>
         <q-card-actions align="right" class="text-primary">
          <q-btn flat label="ok" v-close-popup />
        </q-card-actions>
        </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import gsap from 'gsap'

export default {
  data () {
    return {
      setName: false,
      cosmicDustCount: 0,
      game: {
        starCompanyName: 'Nome da Companhia estelar',
        cosmicDust: 50,
        cosmicDustPerSecond: 0,
        avaliableUpdate: [],
        instaledUpdate: [],
        items: {
          aerogel: {
            label: 'Aerogel',
            img: 'aerogel.png',
            description: 'Material usado para coleta de poeira cosmica!',
            price: 50,
            value: 1.5,
            amount: 0,
            upgrades: {
              nanoGel: { label: 'Nano-Gel', price: 50, description: 'Aumenta a eficiência do aerogel em 1.5', value: 1.5, up: 1 },
              plateNanoGel: { label: 'Placa de Nano-Gel', price: 200, description: 'Aumenta a eficiência do aerogel em 2', value: 2, up: 3 }
            }
          },
          batery: {
            label: 'Bateria',
            img: 'aerogel.png',
            description: 'Material usado para alimentar equipamentos eletrônicos!',
            price: 300,
            value: 5,
            amount: 0,
            upgrades: {
              Placa: { label: 'Material ativo positivo', price: 150, description: 'Aumenta a eficiência da bateria em 10', value: 10, up: 10 },
              PlacaCobre: { label: 'Placa de Cobre', price: 300, description: 'Aumenta a eficiência do bateria em 12', value: 12, up: 15 }
            }
          }
        }
      }
    }
  },

  computed: {
    animatedNumber () {
      return this.game.cosmicDust.toFixed(0)
    }
  },

  created () {
    this.getDustperSecond()
  },

  mounted () {
    if (localStorage.getItem('game-1.0')) {
      try {
        this.game = JSON.parse(localStorage.getItem('game-1.0'))
      } catch (e) {
        localStorage.removeItem('game')
      }
    }
  },

  watch: {
    animatedNumber (newValue) {
      gsap.to(this.$data, { duration: 1.5, cosmicDustCount: newValue })
    }
  },

  methods: {
    addAvaliableUpdate () {
      for (const key in this.game.items) {
        for (const item in this.game.items[key].upgrades) {
          if (this.game.items[key].amount === this.game.items[key].upgrades[item].up) {
            this.game.avaliableUpdate.push(this.game.items[key].upgrades[item])
          }
        }
      }
    },

    buyItem (model) {
      if (this.game.cosmicDust > model.price) {
        this.game.cosmicDust -= model.price
        this.game.cosmicDustPerSecond += model.value
        model.price += model.price * 0.2
        model.amount += 1
        this.addAvaliableUpdate()
      }
    },

    buyUpgrade (model) {
      if (this.game.cosmicDust >= model.price) {
        this.game.cosmicDust -= model.price
        this.game.cosmicDustPerSecond += model.value
        this.game.avaliableUpdate.splice(this.game.avaliableUpdate.indexOf(model.label), 1)
      }
    },

    starCompany () {
      this.setName = !this.setName
    },

    getDust () {
      this.game.cosmicDust++
    },

    getDustperSecond () {
      setInterval(() => {
        this.saveGame()
        this.game.cosmicDust += this.game.cosmicDustPerSecond
      }, 1000)
      // requestAnimationFrame(this.getDustperSecond)
    },

    resetGame () {
      localStorage.removeItem('game-1.0')
    },

    saveGame () {
      const parsed = JSON.stringify(this.game)
      localStorage.setItem('game-1.0', parsed)
    }
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');

@import "node_modules/pixel-borders/src/styles/pixel-borders.scss";

.container {
  padding: 0 30px 30px 30px;
  font-family: 'VT323', monospace;
  font-size: 20px;
  background-image: url(../assets/galaxy_01.jpg);
  background-repeat: no-repeat;
  background-size: cover;

  &__card {
    height: 93vh;
  }
}

.starship {
  border-style: solid;
  border-color: white;
  border-width: 2px;
  border-radius: 10px;
  width: 100%;

  -webkit-box-shadow: inset -1px 3px 8px 5px #1F87FF, 2px 5px 16px 0px #0B325E, 2px 0px 40px 16px rgba(0,195,255,0.6);
  box-shadow: inset -1px 3px 8px 5px white, 2px 5px 16px 0px #0B325E, 2px 0px 40px 16px rgba(0,195,255,0.6);
  background: #CAEDFF;

  &__item-description {
    color: $dark;
    background: #e1e2e4;
    border-radius: 10px;
    -webkit-box-shadow: 0px 5px 14px 5px rgba(0,0,0,0.25);
    box-shadow: 0px 5px 14px 5px rgba(0,0,0,0.25);
  }

  &__items {
    img {
      width: 100px;
      height: 50px;
      border-radius: 5px;
    }
  }

  &__item-list {
    margin: 5px 5px 0 5px;
    border-radius: 10px;
    border-style: solid;
    border-color: cyan;
    background: rgb(5, 0, 107);
  }

  &__item-update {
    border-radius: 5px;
    background-color: white;
  }
}

</style>
