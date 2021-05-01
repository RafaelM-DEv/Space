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
            <q-btn label="contato" style="min-width: 120px;" @click="contact = true"/>
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
    <!-- avisos -->
    <q-dialog v-model="game.info">
      <q-card style="min-width: 100px;" class="flex justify-center">
        <q-card-section class="column q-gutter-y-sm">
          <q-icon name="img:https://i.pinimg.com/originals/45/1a/27/451a27df78f84c8f671ec1e502a4fe97.gif" class="flex self-center robot"/>
          <div class="devDialog font pixel-borders--1">
             Olá, meu nome é Rafael e primeiramente desculpa pelos dados cosmicos perdidos estou trabalhando incansavelmente para chegar a um produto final e você claro é um TESTER, TESTERS são muitos
             importantes no desenvolvimento de um produto sabia? são eles que enchem minha caixa de emails com feedbacks que fazem o produto ser Melhor! vou deixar anotado aqui o que mudei ok!
          </div>

          <div class="font devDialog pixel-borders--1">
            <div class="text-center q-mb-sm text-bold">
              Update log 01/05/2021
            </div>
            <p>>Atualizado modelo do informativo de update</p>
            <p>>Atualizado icone do botão de coleta de poeira cosmica</p>
            <p>>Menu de opções adicionado botão de contato pessoal</p>
            <p>>Adicionado nos itens o total de eficiência</p>
            <p>>Nos itens instalados foi adicionado um contador de nivel de upgrade</p>
            <p>>Todos os itens ganhara um upgrade</p>
          </div>
        </q-card-section>

        <q-card-actions>
          <div class="q-mb-md">
            <q-btn label="ok" @click="close()" class="bg-warning" />
          </div>
        </q-card-actions>
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
          <div style="font-size: 12px;">
            Poeira Cosmica: {{ Math.round(cosmicDustCount) }}
          </div>
          <div style="font-size: 10px;">Por segundo: {{ game.cosmicDustPerSecond }}</div>
          <div class="q-mt-sm" style="font-size: 9px;">Ganho por click: {{ game.click }}</div>
        </div>
        <div class="justify-center flex ">
          <q-btn icon="img:https://i.gifer.com/origin/24/2432cf5ff737ad7d1794a29d042eb02e_w200.webp" color="warning" round glossy @click="getDust()" size="50px"/>
        </div>
        <q-separator class="q-mt-lg" color="black" size="5px" />
        <div class="q-mt-md flex justify-center" style="font-size: 12px;">
          Itens instalados
        </div>
        <div>
          <q-list v-for="(item, index) in game.itemsBuyed " :key="index">
            <q-item>
              <q-item-section>
                <div class="flex justify-between">
                  <div class="q-mt-xs" style="font-size: 13px;">
                  {{ item.label }} +{{ item.ups}}
                  </div>
                  <div>
                    <q-btn label="upgrade" @click="upgrade(item)" size="10px"/>
                  </div>
                </div>
              </q-item-section>
            </q-item>
          </q-list>
        </div>
      </div>

      <!-- upgrade -->
      <q-dialog v-model="upgradeDialog" >
        <q-card class="" style="min-width: 300px;">
          <q-card-section class="q-ma-none q-pa-sm">
            <q-list v-for="(update, index) in upgradesList" :key="index">
              <div class="devDialog font pixel-borders--1">
                <div class="flex justify-between">
                  <div class="row starship__items">
                  <!-- <img :src="require(`../assets/${item.img}`)"> -->
                    <img :src="update.img" style="width: 30px; height: 30px;">
                    <div class="self-center q-ml-sm" style="font-size: 8px;">{{ update.label }}</div>
                  </div>
                  <div class="column self-center text-right align-center">
                    <!-- <div>Preço: {{ Math.round(item.price) }}</div> -->
                    <div style="font-size: 8px;" class="q-ml-lg" >Preço: {{  Math.round(update.price) }}</div>
                    <div style="font-size: 8px;" class="q-ml-lg" >Eficiência: {{ update.value }}/s</div>
                  </div>
                </div>
                <div class="q-px-md q-mt-sm starship__item-description">{{ update.description }}</div>
                <q-btn label="comprar" size="15px" push color="green" class="q-mt-md fit" @click="buyUpgrade(update)" />
              </div>
            </q-list>
          </q-card-section>
<!--
          <q-card-actions>
            <div class="q-mb-md">
              <q-btn label="fechar" v-close-popup class="bg-warning"/>
            </div>
          </q-card-actions> -->
        </q-card>
      </q-dialog>

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
                <div class="self-center q-ml-sm" style="font-size: 9px;">{{ item.label }}</div>
                </div>
                <div class="column text-right">
                  <div>Preço: {{ Math.round(item.price) }}</div>
                  <div>Eficiência: {{ item.value }}/s</div>
                  <div>Total: {{ item.totalEfficiency }}/s</div>
                </div>
              </div>
              <div class="self-end q-mb-xs">Compradas: {{ item.amount }} unidades</div>
              <div class="q-px-md starship__item-description">{{ item.description }}</div>

              <q-btn label="comprar" size="15px" push color="green" :disable="game.openShop <= item.unlocked" class="q-mt-md" @click="buyItem(item)" />

            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </div>

    <!-- company name dialog -->
    <q-dialog v-model="setName">
        <q-card style="width: 300px">
          <q-card-section>
            <q-input v-model="game.starCompanyName" label="Qual nome da sua companhia?" placeholder="Nome da Companhia estelar" />
          </q-card-section>
         <q-card-actions align="right" class="text-primary">
          <q-btn flat label="ok" v-close-popup />
        </q-card-actions>
        </q-card>
    </q-dialog>

    <!-- contato -->
       <q-dialog v-model="contact">
        <q-card class="contact">
          <q-card-section>
            <div class="">
              <div class="text-center text-bold">
                Rafael Martins
              </div>
              <div class="flex">
                 <q-icon class="q-ml-xs" name="ion-logo-facebook" size="30px" />
                 <div class="q-mt-xs q-ml-xs"><a style="text-decoration: none; color: black;" href="https://www.facebook.com/Far3ll274">facebook.com/Far3ll274</a></div>
              </div>
              <div class="flex q-mt-md ">
                 <q-icon class="q-ml-xs" name="ion-logo-twitter" size="30px" />
                 <p class="q-mt-xs q-ml-xs"><a style="text-decoration: none; color: black" href="https://twitter.com/Rafael_M274">@Rafael_M274</a></p>
              </div>

              <div class="flex">
                 <div class="q-mt-xs q-ml-xs fit flex justify-between">
                   <div>
                      <q-icon name="ion-mail" size="30px" />
                      far3ll.274@gmail.com
                   </div>
                   <div>
                    <q-btn flat dense size="11px" icon="ion-copy" @click="copy()" />
                   </div>
                </div>
              </div>
            </div>
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
import { copyToClipboard } from 'quasar'

export default {
  data () {
    return {
      contact: false,
      upgradeDialog: false,
      dialog: false,
      setName: false,
      cosmicDustCount: 0,
      upgradesList: [],
      game: {
        info: true,
        click: 1,
        openShop: 0,
        starCompanyName: 'Nome da Companhia',
        cosmicDust: 0,
        cosmicDustPerSecond: 0,
        itemsBuyed: [],
        upgrades: [
          {
            idu: 1,
            uplink: 'garra',
            label: 'Garra Pro',
            img: 'https://www.flaticon.com/br/premium-icon/icons/svg/3936/3936056.svg',
            price: 200,
            value: 1,
            description: 'Aumenta o ganho do click em 1'
          },
          {
            idu: 2,
            uplink: 'aerogel',
            label: 'Aerogel Pro',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3049/3049596.svg',
            price: 3000,
            value: 3,
            description: 'Aumenta o eficiência do aerogel em +3'
          },
          {
            idu: 3,
            uplink: 'batery',
            label: 'Bateria Pro',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/2333/2333603.svg',
            price: 5000,
            value: 5,
            description: 'Aumenta o eficiência da bateria em +5'
          },
          {
            idu: 4,
            uplink: 'scanner',
            label: 'Scanner',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3270/3270577.svg',
            price: 10000,
            value: 10,
            description: 'Aumenta o eficiência do scanner em +10'
          }
        ],
        items: {
          garra: {
            id: 1,
            label: 'Garra',
            img: 'https://www.flaticon.com/br/premium-icon/icons/svg/3936/3936056.svg',
            description: 'Ferramenta para ajudar na coleta de detritos.',
            price: 20,
            value: 1,
            amount: 0,
            unlocked: 0,
            ups: 0,
            totalEfficiency: 0
          },
          aerogel: {
            id: 2,
            label: 'Aerogel',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3049/3049596.svg',
            description: 'Material usado para ajudar na coleta de poeira cosmica.',
            price: 30,
            value: 2,
            amount: 0,
            unlocked: 2,
            ups: 0,
            totalEfficiency: 0
          },
          batery: {
            id: 3,
            label: 'Bateria',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/2333/2333603.svg',
            description: 'Material usado para alimentar equipamentos eletrônicos.',
            price: 100,
            value: 5,
            amount: 0,
            unlocked: 4,
            ups: 0,
            totalEfficiency: 0
          },
          scanner: {
            id: 4,
            label: 'Scanner',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3270/3270577.svg',
            description: 'Material usado para scanear asteroids.',
            price: 200,
            value: 10,
            amount: 0,
            unlocked: 10,
            ups: 0,
            totalEfficiency: 0
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
    if (localStorage.getItem('game-1.2')) {
      try {
        this.game = JSON.parse(localStorage.getItem('game-1.2'))
      } catch (e) {
        console.log(e)
      }
    } else {
      localStorage.removeItem('game-1.0')
      this.saveGame()
    }
  },

  // meta () {
  //   return {
  //     title: JSON.stringify(this.cosmicDustCount)
  //     // titleTemplate: title => `${title} - Space Clicker`
  //   }
  // },

  watch: {
    animatedNumber (newValue) {
      gsap.to(this.$data, { duration: 1.5, cosmicDustCount: newValue })
    }

    // openShop (newValue) {
    //   console.log(newValue)
    // }
  },

  methods: {
    copy (text) {
      copyToClipboard('far3ll.274@gmail.com')
        .then(() => {
          this.$q.notify({
            message: 'copiado'
          })
        })
        .catch(() => {
          // fail
        })
    },

    buyUpgrade (model) {
      switch (model.idu) {
        case 1:
          if (this.game.cosmicDust >= model.price) {
            this.game.cosmicDust -= model.price
            this.game.click += model.value
            model.price += model.price * 0.2
            this.game.items[model.uplink].ups += 1
          }
          break
        case 2:
          if (this.game.cosmicDust >= model.price) {
            this.game.cosmicDust -= model.price

            this.game.items[model.uplink].value += model.value

            model.price += model.price * 0.4
            this.game.items[model.uplink].ups += 1
          }
          break
        case 3:
          if (this.game.cosmicDust >= model.price) {
            this.game.cosmicDust -= model.price

            this.game.items[model.uplink].value += model.value

            model.price += model.price * 0.4
            this.game.items[model.uplink].ups += 1
          }
          break
        case 4:
          if (this.game.cosmicDust >= model.price) {
            this.game.cosmicDust -= model.price

            this.game.items[model.uplink].value += model.value

            model.price += model.price * 0.4
            this.game.items[model.uplink].ups += 1
          }
          break
      }
    },

    upgrade (model) {
      this.upgradeDialog = true
      this.upgradesList = this.game.upgrades.filter(item => item.idu === model.id)
    },

    toggleDialog () {
      this.dialog = !this.dialog
    },

    buyItem (model) {
      if (this.game.cosmicDust >= model.price) {
        this.game.cosmicDust -= model.price // debita o valor
        this.game.cosmicDustPerSecond += model.value // adiciona o multiplicador do item

        model.price += model.price * 0.2
        model.totalEfficiency += model.value

        if (model.amount === 0) {
          this.game.itemsBuyed.push(model)
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

    close () {
      this.saveGame()
      this.game.info = false
    },

    starCompany () {
      this.setName = !this.setName
    },

    getDust () {
      this.game.cosmicDust += this.game.click
    },

    getDustperSecond () {
      setInterval(() => {
        this.game.cosmicDust += this.game.cosmicDustPerSecond
      }, 1000)
    },

    resetGame () {
      localStorage.removeItem('game-1.2')
      this.$router.go(this.$router.currentRoute)
    },

    saveGame () {
      setInterval(() => {
        const parsed = JSON.stringify(this.game)
        localStorage.setItem('game-1.2', parsed)
      }, 10000)
    }
  }
}
</script>

<style lang="scss">
@import "node_modules/pixel-borders/src/styles/pixel-borders.scss";

.contact {
  width: 300px;
}

.robot {
  width: 100px;
  height: 130px;
}

.devDialog {
    font-size: 10px;
    text-align: center;
    padding: 5px 5px 5px 5px;
    border-radius: 0.5rem;
    border-style: solid;
    border-color: rgb(0, 0, 0);
    // background: #556779;
}

.page {
  background-color: #2A4158;
}

.starshipDesktop {
  background-image: url('https://gifimage.net/wp-content/uploads/2018/11/pixel-gif-stars-1.gif');
  width: 400px;
  background-color: #556779;

  & + &{
    margin-left: 10px;
  }
}

.starship {
  width: 100%;
  background-image: url('https://gifimage.net/wp-content/uploads/2018/11/pixel-gif-stars-1.gif');
  // background-color: #556779;

  &__item-description {
    text-align: center;
    color: $dark;
    background: #e1e2e4;
    border-radius: 3px;
  }

  &__items {
    img {
      width: 40px;
      height: 40px;
    }
  }

  &__item-list {
    border-radius: 0.5rem;
    border-style: solid;
    border-color: rgb(0, 0, 0);
    background: #556779;
  }

  &__item-update {
    border-radius: 5px;
    background-color: #556779;
  }

// upgrades

}

</style>
