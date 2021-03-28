<template>
  <q-page class="container flex row no-wrap">
    <div class="q-mt-md col-6 bg-grey-2 starship">
      <div class="flex justify-center">
        <q-btn outline flat :label="game.starCompanyName" @click="starCompany"/>
      </div>
      <div class="column items-center q-mb-md">
        <div>
          Poeira Cosmica: {{ Math.round(game.cosmicDustCount)}}
        </div>
        <div>Por segundo: {{ game.cosmicDustPerSecond }}</div>
      </div>
      <div class="justify-center flex">
        <q-btn icon="ion-rocket" round  glossy @click="getDust()"/>
      </div>
      <!-- itens -->
      <div class="q-mt-md">
        <!-- TODO loopar os items -->
         <q-list bordered separator class="starship__item-list text-white">
          <q-item v-for="(item, key) in game.items" :key="key" class="starship__items">
            <q-item-section>
              <div>
                <q-btn label="comprar" color="green"  @click="buyItem(item)"/>
              </div>
              <div class="flex justify-between">
                <div>{{item.label}}</div>
                <div>preço: {{ Math.round(item.price) }}</div>
                <div>adicona: {{item.value}}/s</div>
              </div>
              <div class="flex justify-center">
                <img :src="require(`../assets/${item.img}`)">
              </div>
              <div class="q-px-md q-my-md starship__item-description">{{ item.description }}</div>
              <div>Quantidade compradas: {{ item.amount }} unidades</div>
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </div>

    <div class="q-mt-md q-ml-md col-6 bg-grey-2 starship">
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
      game: {
        starCompanyName: 'Nome da Companhia estelar',
        cosmicDust: 50,
        cosmicDustPerSecond: 0,
        cosmicDustCount: 0,
        avaliableUpdate: [],
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

  watch: {
    animatedNumber (newValue) {
      gsap.to(this.$data.game, { duration: 1.5, cosmicDustCount: newValue })
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
      console.log(model.value)
      if (this.game.cosmicDust >= model.price) {
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
        this.game.cosmicDust += this.game.cosmicDustPerSecond
      }, 1000)
      // requestAnimationFrame(this.getDustperSecond)
    }
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');

.container {
  padding: 0 30px 0 30px;
  font-family: 'VT323', monospace;
  font-size: 20px;
  background-image: url(../assets/galaxy_01.jpg);
  background-repeat: no-repeat;
  background-size: cover;
}

.starship {
  border-style: solid;
  border-color: white;
  border-width: 2px;
  border-radius: 10px;

  -webkit-box-shadow: inset -1px 3px 8px 5px #1F87FF, 2px 5px 16px 0px #0B325E, 2px 0px 40px 16px rgba(0,195,255,0.6);
  box-shadow: inset -1px 3px 8px 5px white, 2px 5px 16px 0px #0B325E, 2px 0px 40px 16px rgba(0,195,255,0.6);
  background: #CAEDFF;

  &__item-description {
    color: white;
    background: #0B325E;
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
    border-radius: 10px;
    border-style: solid;
    border-color: cyan;
    background: rgb(2,0,36);
    background: linear-gradient(0deg, rgba(2,0,36,1) 0%, rgba(9,9,121,1) 0%, rgba(0,212,255,1) 57%);
  }

  &__item-update {
    border-radius: 5px;
    background-color: white;
  }
}

</style>
