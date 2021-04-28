<template>
  <q-page class="q-ma-sm">
    <!-- header menu -->
    <!-- TODO Criar nome do jogo como titulo -->
    <!-- <div class="fit q-mt-md">
      <q-btn color="cyan" label="menu">
        <q-menu>
         <q-list>
            <q-item clickable v-ripple>
               <q-item-section>Reset</q-item-section>
            </q-item>
            <q-item clickable v-ripple>
               <q-item-section>Ajuda</q-item-section>
            </q-item>
           </q-list>
        </q-menu>
      </q-btn>
    </div> -->

    <div class="flex">
      <!-- star-dust -->
      <div class="q-mt-md bg-grey-2 starship pixel-borders--1">
        <div class="flex justify-center">
          <q-btn outline flat size="24px" :label="game.starCompanyName" @click="starCompany"/>
        </div>
        <div class="column items-center q-mb-md">
          <div>
            Poeira Cosmica: {{ Math.round(cosmicDustCount) }}
          </div>
          <div>Por segundo: {{ game.cosmicDustPerSecond }}</div>
        </div>
        <div class="justify-center flex">
          <q-btn icon="img:https://www.flaticon.com/svg/vstatic/svg/3413/3413211.svg?token=exp=1619640085~hmac=83b7b0ae0bc95aece0097037cf92cbb1" color="purple" round @click="getDust()" size="50px"/>
        </div>
        <q-separator class="q-mt-lg" />
        <div class="q-mt-md flex justify-center text-h5">
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
      <div class="q-mt-md bg-grey-2 starship pixel-borders--1 q-pb-md">
        <div v-if="game.openShop === 0" class="flex justify-center text-h5">
          <q-btn label="Abrir Melhorias" size="18px" color="warning" class="text-black q-my-sm" @click="open" />
          <div class="flex q-mt-md q-ml-md">
            Preço: 50
          </div>
        </div>
        <!-- TODO mostrar items mais avançados de acordo com o que for comprando -->
        <!-- TODO diminuir o tamanho dos itens -->
        <q-list v-if="game.openShop > 0" bordered separator class="starship__item-list text-white">
          <q-item v-for="(item, key) in game.items" :key="key" class="starship__items q-mt-sm ">
            <q-item-section>
              <div class="flex justify-center text-h5">{{ item.label }}</div>
              <div class="flex justify-between">
                <div>Preço: {{ Math.round(item.price) }}</div>
                <div>Adiciona: {{ item.value }}/s</div>
              </div>
              <div class="flex justify-center">
                <!-- <img :src="require(`../assets/${item.img}`)"> -->
                <img :src="item.img">
              </div>
              <div class="q-px-md q-my-md starship__item-description">{{ item.description }}</div>
              <div>Compradas: {{ item.amount }} unidades</div>

              <q-btn label="comprar" size="20px" push color="green" :disable="game.openShop <= item.unlocked" class="q-mt-md" @click="buyItem(item)" />

              <q-separator color="white" size="3px" class="q-mt-md" />
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
            img: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAOEAAADhCAMAAAAJbSJIAAABPlBMVEX///8dHRsAAAD6uQAbGxndwHz/4ZsZGRcWFhTm5NqFv+mSoNEQEA0UFBEODgv8/PzL5/UHBwDw8PDn5+fu7u7/vgD29vZ1dXTb29vJycllZWSUlJPe3t7Pz88jIyFMTEv/55+ioqGvr6+Ojo1wcG9FRUSDg4JWVlW4uLcAABwqKig+Pj2np6YxMS+2trbDw8JgYF8ADBwAAA0VGBs3NzYMFBwQAAAWDgBjb3Q1LRnwsgAKExwAChx8XxPLlwqCck26ompUSzWlfA9HQC9qX0VObIB7r9VhiKRcY351f6VGS11vnb46Pkt4gqk2RlFUdIpzo8WYp9tWXXWKl8QwMzp2hYybsLmHmaGyytbE3+xNPRhDNhkpJhndpAezhgxtVBXQtXUnIxqQbRJxVxRaRxeadA+dilvDrHh0Z0aXhFcUOrp5AAAbW0lEQVR4nO1deXvbxNavpgoSoKWyHMubZORFdr1ETuwQKIUWWuDCvfDChctedgp8/y/wzmgWSfaM9jblefL7A5o2lufonDn7nLl16wY3uMENbnCDG9zgBje4wT8fHdvthevBJHCc6dRxgmDgj0O3a1/3uppAK1wHI0+3AIJlEFj4Z0NdzoN1+I8ltNUb9FVEh67IEh+yohuI1FEwHnaue70l0fW3kDpNRNoBVMjTZX/g/mOo7E72AOgFqaNQNEjldty+7sXno7PeAEspR12SypX/Yu9L1wHgmDxZ1QysXAigzhHtTijc8xeXk4s5MNLrViNN4s22wWTgr8dnEOO1D+3GdL7XsD49JlQHiuNeNy08+EugJ/mGiNs763BomtzfNzvdhe+sEJ3aAd9lA6zGz3n5eej4RkI8FQNoK+esW+ijdm+w3VuQmwfSKq9fIN3a8T3ApE2xwMY5K6cvzOEimEFepvQO8PwXhcbxhvEPypc3KWjYuuE4TLDZtMd9kBR0SOPmhZDVXkyfDvTCOmKxidTqZpH8y854nlLGCtgXfN6z4/ZwC+gGMsBoXPiLAvIxFWzTn7EHmyQjVeC08p7V8aGEg9XkWVhScwIMRl+/hIp3AKMBzA7+rXM2StJoeWfZz+ruo7cFLalfmoA8hBuqYCB9wxIfXCMCr15//fUr+HkQHP17L0mjAqZ8e4PhGvRXZeCUJiET7S3dMno5+m614YuRr97/4JVXPvTgIwCH+e4IxObDksTi0UaLkK+urhCdYFCaigyMDYO+41FJF2QCWXj14SsIHywVyZjyfmmxSlggsQDO4Sr05b8++uhj5OyDUm86E+0+/X6Qt0+OARl39f4rGP++gnEiV5mYa8uKd6tAUl34svT/fPH5q69+/gkk0eiXXYsIIWWgCoKsTcLFEK7q9Q8Iha98qkigx//FlhOLKlhxVaVjSLL3aoTPP7KgPW5GoXYCxsBVBR8Zvnd5SQl85f+uMraPu2JKV+e9B3OlSlcfvUpI/A98WaUFioch/VoFTEozEKIHJOVLRuGHV5J1rE0poD2ibJQ5m9GGsYnyBaHw1X9dSYb4UcVxZhH1bHgC6coB4qHCKIQbka9q6G9vACPxyBrY8FFfUgJf/eRK0raVlpQClVAZ9Cu6Sm1Lll7/jFL4PqQw244FzJEDswOdZBuy7DEefmFJ6qjammK05kxCq3sQM1VSPiUEfuDJkjXJ/v2FRKMOw0uHZJ0NVMufUAo/uqpP4XBDdKgmVZPQCGNkD4m5+BSKPFjkfMAeUUlVjfTvbjVJ//hzomk+ViR9Xn1ZCD2VbEFrX0srb6D2uPr0w88++9C7guLg5Yu7Q7X3gb45Qy/ro8+xtYDPylBaRTCmGwLU3M8uWq9yhf1SCawLfGTNdGraj0Uvy/ov3Ipf/Bf5BzwPsDjWgPsdVeDHHhl0Vwp9pCdRDxv0EzaqFznxV19+eXWFNk+tbThgBDbg3y4AiNJsigVy1AyDvaSRGpglxDqIliXL2MOqs3kmQGx4KwCGrQYAlhcU95U7TN8Y+0RKdUJzmArwiiW/+IgJbCx3Yg6HJXO/fUqitkwwq9uXcUZkUieX4TdPYBWw3IDuJeWx7Y79s26tXM2Y7cFrzn4FfBJrI2SxRBHF/kwxeSYkDhmBjWYJqoGRqG1ys3BF0dmoTdnBJsAE1VhVid142JJkglXVkzFtNzzDrQqOE0wG67PQrVFBY+oGNJSzoJZe35T+qNkO/WBmxWXDqFGB/LjpDxZ2Jf3Xb865QujScEkvt7M7XX+6F5QI8QNRBXE59bulZc0cMe+miaQF24RhiQ91Qmd/XBcUkLl3ykZinaVOzXN9hUpltLD7CBH2DWAUL+YrBtCDcv7WkL48fVWSnuNHWVjG9MPqghD2QOL1mciKoqqqrquqwinhyxYYLcpIa68xAzYlEl80mdydpspjuJCPtIy0Wc1GCLPVUsF/le5I0cF+XYJG5kZadbztWM0UfFPDftyroCDiZg40Db2u3el0TBMX9OH/Op12d7GezBGdCTLVg2piNvrEhmn18hZ9zEK1kLS3HVanlg2oJRe5WqDdG4ykxJZVwKwwR8wN0Ta5WZ4s2JSFRfSor1lsoarTKypxrdBZxhV8vfi+6tKwcFn0ExwQRaoXyA10Z3GuyPPLeSydcMsqrRIYFY2J6VasE+94WH4K2FUfMAul+RXcRdthNGpWUbGbY9Yr5Z0tChe/JFnLW3JrThmoWZVKGRD2NH5JPElttY4e3CLfWn0nBnhj5eYgXY95UfMaJUp3JvY3z0aaddyLQLJ/ldWpuSdCmqPfzmgqU62ZpDIHLCF7kGF0oFWRVWAcPn+E5bRq6ZcYwzxTMaASam1q15h7qsYjcQpXomoqlN8D35EssapjQzRpDmNYQJrdMlEQ9oo6UQlaFvArrNkbI/gqD/WmYxRhgghzvYAIBE3miSFaM0piTAsMb6z/3blz5ytFlrW0IaJMrCQ9LSKkmcYwYFnGXIvScsPF2dkidLNdnc5GI0+k2x9SoY/uIHwFjl4kZkM1BbAoIOOUQEXUbBDBtMeo9TDG3M945+0laQjziNSfQbq+iii8M1IVKf3bYbQCtXDok8TEytWk1KtQNHHJpxUGMBi2Uu2jOgCjsXDXuiRio0XtMaTwG0zh15YEDjJsWM+BUqQRrKJFKUux/lgwERUSGE49fjAMgyWhs8vcMZd+DfiaUPj60RvHuqZKVc0k1lRc++qyir7g8fZEQB6WQnEeaY61jT6L3u4QSOqeSukRhfg1V0lVE5dNrEE6nkoI5DtN7jZxLkHVWMYtlldhp11LIb4GXjeUJmP+zbfffvMG3DjgQKba0Tq1ColFUqoQp3q2VK1z9dhilAwWwWo6WYdut9tdBKu471DoUJ7hL1eWUbYRaRMdLKMg6+itmLOo97KCRQwiAmRd9O9ss/AY0ZsxN9oAarBIes2mO6LOmbgNbUvcMWwUow6XiK/WcSo/2oiyVz7FP4qkSdjMQzehxtHT9px6qigW5mzS0LNyKCSlEkXBPw4sYOi6Bng9PPhV5znPHCyVrB1srjCPFPk42PWpfMpg6fNfbWeapx9IXEOzC+31dD7vD3hkYIWRaZC5aOPKuEhNsjTqkcq3aU+RZBgZxwl8KKlgJrZExKMq0MU1tKop0y55h/w1DimBRwrf1VhjbT8zl9HTgJNVtcBMlLX8dJaHeJHXV3UMl3hD/LdMZFTdH/7zmikRLy/wzolE2sRZyeUNDmNzeuM4iPpURJ87cDpixB0p89oFBeysFBDTyFyUN4jYVbC4brdNyklHijYmsIGueSxFspRrBqLwonxDGzb4fJM8tYiqPNhoPiOwkVgRO8b5SjKynaL9JAahkKdKXUGqP2QENnO0A0tEvgqZIgqVTdli61hsR2fEFB4801aUJjlI43c9dyNWdGpCIQ8pqw5TJrQwW7ATrwCihLTYb6TAFCplOwNc4T6ckRB8n/5rqmWMmj2sCWCTmFvmxUrXKEvhEGUodY6UMhamNQAt4igNNvKQ0C+vLIRrnIdqLx/BTAczjh4jGbjDPDMrpdapdR0Ah365hZe+VojVHHCPKLt8Y09LqXnHcrrh2B+gyR+F0n+R98+3yQlsK1PIxVTjGvs+ZaFYo5ndQSrhtl3nylXEnVxvhSQUG6KwTVmY3qA2rQIJ33fLXwGQSrjBaG+aE9RF6b7cTCGOZJuikLgthywc0DqciIUDL00e/nUj51xYVFuSvewlmSulmqbhY0lSREPuX4vqcK7MGjR2CIk29sxjDZHelnNyoS385VYzFLr8uNTlyy6FT+nbnb/15ne/f/f9D+f3L2lJNcu64DBUEKNS2FF/jqw2QyHNLRy4OiQ9Lqj3047Q3d3v3nty7/bp6e17T378bXefJHo2YkHFRjZnh+HXUCUVxQGpmR5RQkupXI+bRhznb75z+/Q2wenpvfd2b+FPiUvM7SIUYgEq73lnPIxa9bZtJhciqHFRCb7/dkwfJvL2T+dZso0eHLWP5aTR1pnZiJLA0ojztN0RANIgenE9kr/lKT0TR3mQwDR9EY1vR1y0hOFyIQrxouoe6SIY6Uxj9oCm7FQwQ/vbzyhxkNT57rsj+iDuvbmTsk52tY1sHkfA/mKVtP4xSIYP6ZmOpO4e/bW5NFbsO7hJI8rCt54csxAy8b1IToVhvF0g2WuS1HUj0wawr6+gsMkHu8cnJyd/XSIvBn8Hd6FkF15yZDRiIqZQ5AkRCjN92A5OGuV6r4WAJT5KK2y1i58hhSePVCXuSuHoa+LsnL/Dp/A0ElPh+3cL6FIStjVz1IWkhpAx3Ks7RODJg0vQbUUpWUm2OB/BO1f+5R6XwNunP+2ytAQJEDMt3SLeOvWBTWvkRM11zENIYc/GLcHKnveR6J923/NZSLSp0LcmRZfMRQ1IkaeJeRE4BsQaE+7DdyGJD3+RgUtS/DxOkELy7vckhafxD6e/ZlIY4AxM5qq2emzB6uIsUS9qKcrOe/fRpaJKNPrlGYs2h8J77/1KduUpoVDY3TkqUPzEbQ3VmjEOgRUN0d0hMNSdqhhQ/gmFPH1BKXwzwbZ37p7/gLbl6ZNf752+fZllywoUsG1+OFcNUbZAVog4DPtLTV86Nstg8AY2ECmVpXuEaej/3+/u/wEpfufu3d9Pf99JGUEXyLQlEaiiaeRQXaRKE7utMxxGviChkLtOEvu/9SQi8Mfv7z6B/z3fvYl+/EW+++P3u4zlYY8zu9sqMJpTpZ2loIqVwUPqDJz/iMT03t3d5W/wDxIkLdqD8g+ZyyMJmEyXhvhMhx0aBdA6mwR+6ptxpMmRmaEl3Ic0csSqBsnkXRgg/nEemY97dyVsSAXlpQ5WIpmLz7JUmWg56KgZSI2f6Yqy4DY+M8HVCDR/fB+pT6hkpPP34Ga8lKG03sabUFwixFssO2ggzy9dAbY9eiIvsUVckRuMy8x8nd/B7QDQ5keq5vsd+gOUT+SoEr9b6HDhTG+2w3nQ0FAU5kpDdXjU0iTHn+2J3GA0YEQStcw7JJF6jnzv0/fuRy7qk3OkXbHLJskqX0hpr0Rm/ZA6/SXtPYrprP3XX329sSSFfX8kNLLKcYNJrYbrXdFkOI7x7/0iI10D5RNK6x938ccEtgJr0mxnxS7csZEGjPe0N6JOuTesuB15jJOXnJQWbSbm8oJmw6Xz756cnv52GXHvx/u7N9/GMioL3j+JLLNNOW1NK1mShTGlrJCGzpXKXmL0TpUlhwwnyybZbMjH7vynd/54S7r/4+npk528u5QydyHRIdk7jMbeJd1uSKE6uxO3rJKNEDn6Cu+IOG1555tmVt1HNO6Q0rn35Lcd/SvhaWUs+tmza0xNrOMysdUYhd/EndVrIYWLbJWdGAOJ6bx795L+2RoJrF2Y4Sgd/lL5voHAkpVv7xz2joullKQqhK9yekBiDDAXsYj6KpnxPemftEoXZXpx2zFqyiU7QaxpiEYTV9wHqdmyDBldwuS8T045Em/xCoeCoAeqLBF9324tSd2T1xwKrcUtchJQ7EF2R4eDsKNOb6Gpa3n8ClAavSLKiA+oOrT5V1/9D00RY593xYkvandTatF1lgDs6WksdyoBix3NR4fURxkxg0POlGWnCLEOr3TCsgPZr0OfRk5OFBJ6bcy/TnqmnWnUxA49I+r3tcNgpJH677KfdeKCqi5ZydxgHczoav1JRMPLVsICDHECmteJQNuyE9WR6KQd8rtSpwk77W6v5w472bEOnaWSs3b8HgSzQXOBYgsApCDx6XYU63C/lZ0XZjYfHaZRgbc0tPL7xFaw0OcN7iIdClVTwV0/GITpt4MjYK76w9FFbBFNqFfU1eLll8MtkNSMCiEHQzISQsnpAbJJ72KD9wxEXga/zcyhp9oJLWi8pfFyBOhYlZrPEeoFB3EMCmRxSiKK1/i+Pp0jRfUQ1OOGgyl82ZDLpPom7BhpngLBxW2rqSk8CL64ikA3IpVhF7lChMK5WjyLErJhpbm8GR82TLXHE7ixaiWGw4ymWhJAyYQWSLEREAodrehhiF484D1/lEqkFeKBJK0p1I3owpo6acVhRkcr6/w+o9+vbAiFe6VQr0t7PWPniAoMR/XT3aEmZb5SqwEbF8y5aS2bOGU0BQ/VAFhEBE5Avtdhdv1RomPK0HPtyzD6vtivRXNADGAg/7dOhzLJX3JFfU7ncODFQTFVZ5A+F4VOgn5FNOPEHoZ+MDeSDWEKmOey3IyOqavMw0D+FpiG4WIOKrsACIOMjUhPW9LkH8qE7PtzGZoRrtwM5/PZauMdDapRwLJArROfzo2TVIEFfdhIZOCfahRLM89gsKE/mInI81I0uHaF3xXtAzRu6CDekDVIXwG9S/Qok1FzpSp7tu1rVGk6UdJA8bhroLqGejADJHiyDmS+cgss6RA60PuFzmdhpZ5w6mxLMiZUdRuqKG9QAKQbl5txatHhUNSSjVcA6HPB4TUajjDuwa04XxeL1XuRllESgYfN9NrLL/tAWVU3ijjyFuRO2EBDGkKabVusMtjIXnxxkj4dF01F9PDZ26RTB+0YCBuhcJjVQtaWFCqnBb5hCMj0PUtbBeMSxyND7NalXALEwzWhcKrVao+aZR3UYWlDY15gI/hTJxis8yYsHGFNjrSlEjhtRda2mMBQkWtNvMa7R6BNzSWbDt/YoZKjBWACjVFaTEaqZERMXGzUetVScp5MkIhnB58aObvGgbklecvDjN8ERWvzwBkZSvURnRhy5rHHKVOQz2TUaXdJOnWPDh9HeQ/VQFebGQUmvGeBpLcEs6daS/UZctEnrl1qEDTBgP4bmNfsFaYpSoGo9+LsdlPjUynaW+I1GRueclrgsxzz+kd2LHFFG2GQIPHwMopaGCukSmftBY9t98JGrjIlR6wtUbqzH5OoZVzTVBI2G5cGiliiWnBzakImnYKETH9Dw7HNgUVj4+cwrJmUZkXFdygsm/gaQxlMG5CbBbv8rM7FKMVBPBdxpqjtJQpNhlyhbJJC4n4yI2OUUYMg3TMiq38LDXqKBRWFh3WOXHW3iVkv+bF/M+hrOUy81Z4lQyNDqTIqMoLbj+d+1h3oVwI9ek5NvMU622TVVwbLSkOpw3k881MGm3rTdEuBnl/LUmuT1M3HSrHkRBLo9tZ4P2vPd6L/mBa1Mw/WeYnNGNFY4j5qs5e6HFqtNVKzAsgJh5wKbXx/IKVR7YeFGNmdrJI1cbnUAOVmwMbvZG+NMzVNo6wBz8kjshcsU+PrlOORns8BbaXYGPPOxDjIqEUJJ9/l14A7rr8F6ZvZoXAPnrWXxgVNBebmXm0HHGUNdQCMkTNYdIdtTKnZsYc9P5jrB7c6o26N67rW2SaTDQvcJ2YHnAvl8b3khrdZzSBWG3TXj5Ee0Y5ugio3iL5ZMCYWcIQ7/kYwWxDP2ucN2lcM4JW8TKBhtFhutFCS2nU2B9Mvs6BaYDktpnafIWj693gWFh+dHprxmX8lBFJF+6DXZOxcES0qWcXvvTDtseNFG45z/QOU2GhvSs7YrsS97rZppRRfpFcqoum4Y2e0kVC226L3zkSpb3kzcta9yotsA6Ppe7np8de4s68E7K67WA8CfHdQMPAXbrca5xii7qFGplHHiOdePZvsbynQ00/N3plG55c2OnSnGsidW3LpSUrZaMfXPTR6yVsFOHmZlYpgtSa9mXkGlUFktJlzpCmMtBdiK7bpdOzmL/brsujoOm8NNGcakaRn8PD4KEX5EbAJmF0/mDqTwnXuNIJi0WoOumNouwbHztSM9WnVuJgeFVWsqOa9rZAPZW2RpUe0JhDuoyXAMOgw9xkf+dErGH6M+H54SQNOWZ1FO6b1OpchBvEV9cahQl4wOTUqdrFEQqZeXlxcoPFRQFRZEqAt03bUGunwyNjsLtAS0JP6aUKCeCtWKhiiVyRfPHrw8OHTx95l2YspzTmdE1fDFCIXW7l49+mffz59jGg8JIRtxUo2w5QUSb58Gs3YOPn5r4uST6HnjXhTxYsCHb1QvT9fi3Dy+OLodbXioK9C6Qu9v8uHJxR/X5SSN3a9RvmTTzGgQZB3J6+9hPHag4sjJ82Nc4bl9dlMlXZ/ncR4cJk7ejG1tgY8Y7iEi6eUQEji491RdXScqGyXJBFJCB6SQvDzL8U6ihGYlqtlKGxZlncvxXjt5FI+6vqaJEgsJ6hdKCHSSRJ/7YpyhF3DXG8mLFzC7vFrSRIhE492SuIMZTl104OPf5Si8OFFQb3IbrHU6zncLpAuH6QofHrB8UITZygPzUnOMo8ovBQcyzn6JK18q/WqNscU/sl7yWaiBQPMir9TdLbGO6KwgBj0aPJfruUR3+JJKaSQcyDBnMckGsvCPjCaX3ORohCKSIGxXSHLo9dOMNiqrHgvJSmEJoub1EqUfdXiX+spUFUnKUTbPDfbMm4ybEPnQU4ONQ1PMMzEXjy6AU4Ix5B27yatBdStuafr48arJhKIU0O6+DthD0+gyRdESrGLWrxvAmXsLhNMfHcXH4ATwWmUwGgJF7FP89KjnXgKxSBREdUKHsnTZEl+6wHl4KPLXMFrsXsg5YZSwOjcxO7Pl4hj+miXdTBwnCi/yGBahI2R53XhPXj68OHfkWefE+j1pPhwVEPZUR+HN3//+fPPDx9fwiUYGT24brI/wfKKLCGyMzKLzpTsZETc46FqdbutGKYsPoxiVEPOMnexDEWrLXDzLdRQCcYbmZHFMH66ITfYnjGJG3fQonOC8ElyrLUOnPy4JlwBS1dkGR3Sn2Y8vROwDhsZbBtN0bpbVBNT0d1pm/zN3Vumer5AkE+jG8z3nrffDjJ+1Rx7jIFq8w1E9jiYz2b9SaFDqJ1psnIvA8Up4ON0Wq2sZ3fW+/ihYPNcehSzEHrJThq4uWr2HAwDPZmQq3qpcpMwA5Bq+tIBcMKKpWt7PQOWHD9p+3w7wITobtOD9CEjte2g7BmgljuYJ8+XamBeM5RoEr39wWUBMuSkNHPGbjFmtnuD+RIkGhRVAPrXvgHTWKyOJu/gXiFpPhmHQ+4NIejiELs3nqAOKaDHGgv+OBOdZbxOhKP0fmTcjLoTpNXWCSYDfz0+Ozsbj9f+YBL0R8ujs8HR3bOZ416uE67D6fpiDNVJJwaFZWjqwW/L0A/YluhNvQa0/A3gXN9RBAoaAzQ9ewGF8xDdAToTzOkTyqIO8m4596+1ua0Uhv5WAUArRKWMrtbdT/2CSvfFQas32KLNpvG6vjBpSnRtsLwdLOxraiytD3sx6G90A6sVQ4tAzjwDQ91vJ0XN5QsO2+0t/Ekwnfa3234fnXkeh73uczoSc4Mb3OAGN7jBDW5wgxvc4Nng/wGIz0ipq2J68AAAAABJRU5ErkJggg==',
            description: 'Material usado para coleta de poeira cosmica!',
            price: 30,
            value: 1.5,
            amount: 0,
            unlocked: 0
          },
          batery: {
            id: 2,
            label: 'Bateria',
            img: 'https://www.flaticon.com/svg/vstatic/svg/2254/2254783.svg?token=exp=1619580843~hmac=0ffc5c245b897ebbad1f34d702279579',
            description: 'Material usado para alimentar equipamentos eletrônicos!',
            price: 50,
            value: 5,
            amount: 0,
            unlocked: 4
          },
          scanner: {
            id: 3,
            label: 'Scanner',
            img: 'https://www.flaticon.com/premium-icon/icons/svg/3270/3270577.svg',
            description: 'Material usado para escanear asteroids',
            price: 100,
            value: 5,
            amount: 0,
            unlocked: 7
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
    // this.saveGame()
  },

  // mounted () {
  //   if (localStorage.getItem('game-1.0')) {
  //     try {
  //       this.game = JSON.parse(localStorage.getItem('game-1.0'))
  //     } catch (e) {
  //       localStorage.removeItem('game')
  //     }
  //   }
  // },

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
@import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');

@import "node_modules/pixel-borders/src/styles/pixel-borders.scss";

.starship {
  width: 300px;

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
    border-color: rgb(0, 0, 0);
    background: rgb(135, 2, 153);
  }

  &__item-update {
    border-radius: 5px;
    background-color: white;
  }
}

</style>
