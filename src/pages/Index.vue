<template>
  <q-page class="container row bg-black">
    <div class="q-mt-md col-3 bg-cyan-1">
      <div class="flex justify-center">
        <q-btn outline flat :label="game.starCompanyName" @click="starCompany"/>
      </div>
      <div class="column items-center q-mb-md">
        <div>Poeira Cosmica: {{ Math.round(game.cosmicDust) }}</div>
        <div>Por segundo: {{ game.cosmicDustPerSecond }}</div>
      </div>
      <div class="justify-center flex">
        <q-btn icon="ion-rocket" label="Pegar poeira" glossy @click="getDust()"/>
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
export default {
  data () {
    return {
      setName: false,
      game: {
        starCompanyName: 'Nome da Companhia estelar',
        cosmicDust: 0,
        cosmicDustPerSecond: 0.3
      }
    }
  },

  created () {
    this.getDustperSecond()
  },

  methods: {
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
.container {
  padding: 0 30px 0 30px;
  // background-color: $dark;
}
</style>
