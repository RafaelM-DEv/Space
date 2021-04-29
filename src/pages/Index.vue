<template>
  <q-page class="q-pa-sm page font">

    <div class="fit q-mt-sm justify-center flex">
      <q-btn color="warning" label="opções" :class="isMobileOptions" @click="toggleDialog" />
      <q-dialog v-model="dialog">
      <q-card style="min-width: 100px;" class="flex justify-center">
        <q-card-section class="column q-gutter-y-md">
          <div>
            <q-btn label="Reset" @click="resetGame" style="min-width: 120px;" color="negative" />
          </div>
          <div>
            <q-btn label="ajuda"  style="min-width: 120px;"/>
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
    </div>

    <div class="flex justify-center">
      <!-- star-dust -->
      <div :class="isMobile">
        <div class="flex justify-center">
          <q-btn outline flat size="15px" :label="game.starCompanyName" @click="starCompany"/>
        </div>
        <div class="column items-center q-mb-md">
          <div style="font-size: 11px;">
            Poeira Cosmica: {{ Math.round(cosmicDustCount) }}
          </div>
          <div style="font-size: 10px;">Por segundo: {{ game.cosmicDustPerSecond }}</div>
        </div>
        <div class="justify-center flex">
          <q-btn icon="ion-rocket" color="deep-purple-6" round glossy @click="getDust()" size="50px"/>
        </div>
        <q-separator class="q-mt-lg" color="black" size="5px" />
        <div class="q-mt-md flex justify-center" style="font-size: 12px;">
          Upgrades instalados
        </div>
        <div>
          <q-list v-for="(item, index) in game.itemsBuyed " :key="index">
            <q-item>
              <q-item-section>
                {{ item }}
              </q-item-section>
            </q-item>
          </q-list>
        </div>
      </div>

      <!-- itens -->
      <div :class="isMobile">
        <div v-if="game.openShop === 0" class="flex" style="height: 100%;">
          <q-btn label="Abrir Melhorias - Preço 50" icon="ion-planet" size="12px" color="warning" class="fit" @click="open"/>
        </div>
        <q-list v-if="game.openShop > 0" bordered separator class="starship__item-list text-white" style="font-size: 8px;">
          <q-item v-for="(item, key) in game.items" :key="key" class="starship__items q-mt-sm">
            <q-item-section class="row">
              <div class="flex justify-between">
                <div class="row">
                  <!-- <img :src="require(`../assets/${item.img}`)"> -->
                  <img :src="item.img">
                <div class="self-center q-ml-sm" style="font-size: 10px;">{{ item.label }}</div>
                </div>
                <div class="column self-center text-right align-center">
                  <div>Preço: {{ Math.round(item.price) }}</div>
                  <div>Adiciona: {{ item.value }}/s</div>
                </div>
              </div>
              <div class="self-end q-mb-xs">Compradas: {{ item.amount }} unidades</div>
              <div class="q-px-md starship__item-description">{{ item.description }}</div>

              <q-btn label="comprar" size="15px" push color="green" :disable="game.openShop <= item.unlocked" class="q-mt-md" @click="buyItem(item)" />

            </q-item-section>
          </q-item>
        </q-list>
      </div>

      <!-- upgrades -->
      <!-- <div class="q-mt-md q-ml-md bg-grey-2 starship pixel-borders--1 ">
        <q-list bordered separator class="starship__item-list">
          <div class="flex justify-center">
              Upgrades
          </div>
          <q-item>
            <q-item-section>
              <q-list bordered separator class="starship__item-update">
                <q-item v-for="(upgrade, index) in game.upgrades" :key="index" clickable @click="buyUpgrade(upgrade, index)">
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
      </div> -->
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
      dialog: false,
      setName: false,
      cosmicDustCount: 0,
      game: {
        openShop: 0,
        starCompanyName: 'Nome da Companhia estelar',
        cosmicDust: 0,
        cosmicDustPerSecond: 0,
        // upgrades: [
        //   { label: 'Nano-Gel', price: 50, description: 'Aumenta a eficiência do aerogel em 1.5', value: 1.5, up: 1 },
        //   { label: 'Placa de Nano-Gel', price: 200, description: 'Aumenta a eficiência do aerogel em 2', value: 2, up: 3 },
        //   { label: 'Material ativo positivo', price: 150, description: 'Aumenta a eficiência da bateria em 10', value: 10, up: 10 },
        //   { label: 'Placa de Cobre', price: 300, description: 'Aumenta a eficiência do bateria em 12', value: 12, up: 15 }
        // ],
        itemsBuyed: [],
        items: {
          aerogel: {
            id: 1,
            label: 'Aerogel',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3049/3049596.svg',
            description: 'Material usado para coleta de poeira cosmica!',
            price: 30,
            value: 2,
            amount: 0,
            unlocked: 0
          },
          batery: {
            id: 2,
            label: 'Bateria',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/2333/2333603.svg',
            description: 'Material usado para alimentar equipamentos eletrônicos!',
            price: 100,
            value: 5,
            amount: 0,
            unlocked: 4
          },
          scanner: {
            id: 3,
            label: 'Scanner',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3270/3270577.svg',
            description: 'Material usado para escanear asteroids',
            price: 200,
            value: 10,
            amount: 0,
            unlocked: 10
          }
        }
      }
    }
  },

  computed: {
    animatedNumber () {
      return this.game.cosmicDust.toFixed(0)
    },

    isMobile () {
      return this.$q.screen.lt.sm ? 'q-mt-md starship pixel-borders--1 text-white' : 'q-mt-md pixel-borders--1 starshipDesktop no-wrap text-white'
    },

    isMobileOptions () {
      return this.$q.screen.lt.sm ? 'full-width' : ''
    }

  },

  created () {
    this.getDustperSecond()
    this.saveGame()
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
    },

    openShop (newValue) {
      console.log(newValue)
    }
  },

  methods: {
    // addAvaliableUpdate () {
    //   for (const key in this.game.items) {
    //     for (const item in this.game.items[key].upgrades) {
    //       if (this.game.items[key].amount === this.game.items[key].upgrades[item].up) {
    //         this.game.avaliableUpdate.push(this.game.items[key].upgrades[item])
    //       }
    //     }
    //   }
    // },
    toggleDialog () {
      this.dialog = !this.dialog
    },

    buyItem (model) {
      if (this.game.cosmicDust > model.price) {
        this.game.cosmicDust -= model.price // debita o valor
        this.game.cosmicDustPerSecond += model.value // adiciona o multiplicador do item

        model.price += model.price * 0.2 // aumenta o valor do item apos comprado

        if (model.amount === 0) {
          this.game.itemsBuyed.push(model.label)
        }

        model.amount += 1 // adiciona quanditdade de items comprados
        this.game.openShop += +1
      }
    },

    open () {
      if (this.game.cosmicDust >= 50) {
        this.game.cosmicDust -= 50
        this.game.openShop = 1
      }
    },

    // buyUpgrade (model, index) {
    //   console.log(index)
    //   if (this.game.cosmicDust >= model.price) {
    //     this.game.cosmicDust -= model.price
    //     this.game.cosmicDustPerSecond += model.value
    //     this.game.upgrades.splice(index, 1)
    //   }
    // },

    starCompany () {
      this.setName = !this.setName
    },

    getDust () {
      this.game.cosmicDust++
    },

    getDustperSecond () {
      setInterval(() => {
        this.game.cosmicDust += this.game.cosmicDustPerSecond
      }, 1000)
    },

    resetGame () {
      localStorage.removeItem('game-1.0')
      this.$router.go(this.$router.currentRoute)
    },

    saveGame () {
      setInterval(() => {
        const parsed = JSON.stringify(this.game)
        localStorage.setItem('game-1.0', parsed)
      }, 10000)
    }
  }
}
</script>

<style lang="scss">
@import "node_modules/pixel-borders/src/styles/pixel-borders.scss";

.page {
  background-color: #2A4158;
}

.starshipDesktop {
  width: 400px;
  background-color: #556779;

  & + &{
    margin-left: 10px;
  }
}

.starship {
  width: 100%;
  background-color: #556779;

  &__item-description {
    text-align: center;
    color: $dark;
    background: #e1e2e4;
    border-radius: 3px;
  }

  &__items {
    img {
      width: 50px;
      height: 50px;
    }
  }

  &__item-list {
    // margin: 3px 3px 3px 3px;
    border-style: solid;
    border-color: rgb(0, 0, 0);
    background: #556779;
  }

  &__item-update {
    border-radius: 5px;
    background-color: #556779;
  }

}

</style>
