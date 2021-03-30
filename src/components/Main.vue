<template>
  <div class="main row">
    <div class="col-xl-5 col-sm-12 inputs">
      <div class="inputs__input">
        <label for="url" class="inputs__input__title">
          URL
        </label>
        <input id="url" type="text" placeholder="np. https://www.marketingmatch.pl" v-model="inputs.url">
        <br>
        <button type="button" class="btn btn-light btn-sm url-btn" @click="getData()">Pobierz meta dane</button>

      </div>
      <div class="inputs__input">
        <label for="title" class="inputs__input__title">
          Tytuł
        </label>
        <p class="inputs__input__count">{{ inputs.title.length }} znaków (<span
            v-bind:class="[getTitleWidth() > 600 ? 'error' : '']">
          {{ getTitleWidth() }}
        </span>/600px)</p>
        <b-progress class="mt-2" :max="100">
          <b-progress-bar :value="(getTitleProgress())"
                          :variant="getTitleProgress() > 100 ? 'danger' : 'success'">
          </b-progress-bar>
        </b-progress>
        <input id="title" type="text" placeholder="Postaraj się zmieścić się w 600px aby wyświetlić całość"
               v-model="inputs.title">
        <div v-if="getTitleWidth() > 600" class="inputs__input__error">
          Tytuł jest szerszy niz 600px i może nie być wyświetlany w pełnej długości.
        </div>

        <br>
        <button type="button" class="btn btn-light btn-sm" @click="capitalizeTitle">Tytuł z dużej litery</button>
      </div>
      <div class="inputs__input">
        <label for="desc" class="inputs__input__title">
          Opis
        </label>
        <p class="inputs__input__count">{{ inputs.desc.length }} znaków (<span
            v-bind:class="[getDecsWidth() > 960 ? 'error' : '']">
          {{ getDecsWidth() }}
        </span>/960px)</p>
        <b-progress class="mt-2" :max="100">
          <b-progress-bar :value="(getDescProgress())"
                          :variant="getDescProgress() > 100 ? 'danger' : 'success'"></b-progress-bar>
        </b-progress>
        <textarea id="desc"
                  placeholder="Meta opis może być przycięty na ~960 pikselach (w wersji desktopowej) i na ~680px w wersji mobilnej"
                  v-model="inputs.desc"> </textarea>
        <div class="inputs__input__error" v-if="getDecsWidth() > 960">
          Meta opis może być przycięty na ~960 pikselach (w wersji desktopowej) i na ~680px w wersji mobilnej
        </div>
      </div>
      <div class="inputs__input">
        <label for="bold" class="inputs__input__title">
          Słowa kluczowe
        </label>
        <input id="bold" type="text" placeholder="Podaj słowa kluczowe rozdzielające je spacją" v-model="inputs.bold">
      </div>
      <div class="inputs__input--checkbox">
        <p class="inputs__input__title">
          Opcje
        </p>
        <label class="inputs__input--checkbox" for="date">
          <input id="date" type="checkbox" @click="showDate()"> Data
        </label>
        <label class="inputs__input--checkbox" for="rating">
          <input id="rating" type="checkbox" @click="showStars()"> Ocena
        </label>
        <label class="inputs__input--checkbox" for="ads">
          <input id="ads" type="checkbox" @click="showAds()"> Reklamy
        </label>
        <label class="inputs__input--checkbox" for="map">
          <input id="map" type="checkbox" @click="showMap()"> Mapa
        </label>
      </div>
      <div class="inputs__input--buttons">
        <button class="btn btn-light btn-sm" @click="reset()">Reset</button>
        <button class="btn btn-primary btn-sm" @click="saveImg()">{{ captureBtn }}</button>
        <button class="btn btn-primary btn-sm" @click="copy()"> {{ copyBtn }}</button>
      </div>
    </div>
    <div id="canvas" class="col-xl-7 col-sm-12  results">
      <div class="separator"></div>
      <div class="results__header">
        <div class="row">
          <div class="col-2 results__header__logo">
            <img src="../assets/img/logo.png" alt="">
          </div>
          <div class="col-10 results__header__main">
            <div class="results__header__main__input">
              <div class="results__header__main__input__icons">
                <i class="fas fa-times fa-lg"></i>
                <i style="border-left: 1px solid #dfe1e5;padding-left: 5px;" class="fas fa-keyboard fa-lg"></i>
                <i class="fas fa-microphone fa-lg"></i>
                <i class="fas fa-search fa-lg"></i>
              </div>

            </div>
            <div class="col-12 results__header__main__menu">
              <div class="results__header__main__menu">
                <div class="row">
                  <div v-for="item in menuItems" :key="item.message"
                       class="results__header__main__menu__item">
                    <p class="results__header__main__menu__item-item"
                      v-html="item.message">

                    </p>
                  </div>
                  <div class="col-6"></div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="results__content">
        <div class="row">
          <div class="col-2"></div>
          <div class="col-10">
            <p class="results__content__search">
              Około 608,000,000 wyników (0.54 sekund)
            </p>
            <div class="results__content__ads" v-bind:class="[{ 'show' : isAd}]">
              <div class="results__content__url results__content__url--ad">
                marketingmatch.pl
              </div>
              <div class="results__content__title">
                Agencja interaktywna Marketing Match - SEO, SEM, WWW - Warszawa
              </div>
              <div class="results__content__desc">
                Jesteśmy certyfikowanym partnerem Google!
                Oferujemy profesjonalne usługi w zakresie optymalizacji, marketingu internetowego i kompleksowej obsługi
                stron.
              </div>
            </div>
            <img v-bind:class="[{ 'show' : isMap}]" width="600" class="results__content__map"
                 src="../assets/img/map.png" alt="">
            <div class="results__content__metadata">
              <div class="results__content__url results__content__url--metadata">
                <p v-if="inputs.url === ''">{{ showUrl() }}</p>
                <p v-else>{{ this.inputs.url | url }}</p>
              </div>
              <div id="metaTitle" class="results__content__title results__content__url--metadata">
                <p class="results__content__title--title" v-if="inputs.title === ''">{{ content.title }}</p>
                <p class="results__content__title--title" v-else>{{ inputs.title }}</p>
              </div>

              <div class="results__content__desc results__content__url--metadata" id="metaDesc">
                <span class="results__content__desc--date"
                      v-bind:class="[{ 'show' : isDate}]">Lut 20,2021 - </span>
                <span class="results__content__desc--desc" v-if="inputs.desc === ''">{{ content.desc }}</span>
                <span class="results__content__desc--desc" v-else
                      v-html="$options.filters.bold(this.inputs.desc, this.inputs.bold)"></span>
                <br>
                <div class="results__content__stars" v-bind:class="[{ 'show' : isStar}]">
                  <img src="../assets/img/stars.png" alt="">
                  Ocena 5/5 - 112 głosów
                </div>
              </div>
            </div>
            <div class="separator"></div>
            <div class="results__content__blank">
              <div class="results__content__blank__url"></div>
              <div class="results__content__blank__title"></div>
              <div class="results__content__blank__desc"></div>
            </div>
            <div class="separator"></div>
            <div class="results__content__blank">
              <div class="results__content__blank__url"></div>
              <div class="results__content__blank__title"></div>
              <div class="results__content__blank__desc"></div>
            </div>
            <div class="separator"></div>
            <div class="results__content__blank">
              <div class="results__content__blank__url"></div>
              <div class="results__content__blank__title"></div>
              <div class="results__content__blank__desc"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div id="output"></div>

  </div>
</template>

<script>
import html2canvas from 'html2canvas';
// import axios from "axios";
import $ from 'jquery'

export default {
  data() {
    return {
      menuItems: [
        {message: '<i class="fas fa-search"></i> <span>Wszystko</span>'},
        {message: '<i class="fab fa-youtube"></i> <span>Wideo</span>'},
        {message: '<i class="far fa-image"></i> <span>Obrazki</span>'},
        {message: '<i class="far fa-newspaper"></i> <span>Wiadomości</span>'},
        {message: '<i class="far fa-map"></i> <span>Mapy</span>'},
        {message: '<i class="fas fa-ellipsis-v"></i> <span>Więcej</span>'}
      ],
      content: {
        url: 'przykładowy/url',
        title: 'To jest przykładowy tytuł',
        desc: 'Tu jest przykład jak snippet będzie wyglądał w wyszukiwarce Google. Zawartość, która się pojawia tu, zazwyczaj jest brana z meta opisu strony.'
      },
      inputs: {
        url: '',
        title: '',
        desc: '',
        bold: '',
      },
      isDate: false,
      isMap: false,
      isStar: false,
      isAd: false,
      allData: '',
      copyBtn: 'Skopiuj HTLM',
      captureBtn: 'Pobierz zrzut ekranu',
      response: '',
    }
  },
  watch: {
    response: function (val) {
      const title = val[0]
      const desc = val[1]
      this.inputs.title = title
      this.inputs.desc = desc
    },

  },
  filters: {
    bold: function (value, bold) {
      let string = value;
      const boldStr = bold.split(' ');
      boldStr.forEach(key => {
        if (string.includes(key) && bold !== ' ') {
          string = string.replace(key, key.bold())
        }
      })
      return string
    },
    url: function (val) {
        let string = val
        if (string.slice(-1) === '/'){
          string = string.slice(0, -1)
        }
        string = string.replace('https://www.', ' ')
        string = string.replace('www.', ' ')
        string = string.replace('/', '>')
        return string
    }
  },
  methods: {
    showUrl: function () {
      return this.content.url.replace('/', ' > ')
    },
    showDate: function () {
      this.isDate = !this.isDate
    },
    showMap: function () {
      this.isMap = !this.isMap
    },
    showStars: function () {
      this.isStar = !this.isStar
    },
    showAds: function () {
      this.isAd = !this.isAd
    },
    getTitleWidth: function () {
      if (this.inputs.title === '') {
        return 0
      } else {
        return document.querySelector('.results__content__title--title').offsetWidth;
      }
    },
    getTitleProgress: function () {
      if (this.inputs.title === '') {
        return 0
      } else {
        return ((document.querySelector('.results__content__title--title').offsetWidth) / 600) * 100;
      }
    },
    getDecsWidth: function () {
      if (this.inputs.desc === '') {
        return 0
      } else {
        return document.querySelector('.results__content__desc--desc').offsetWidth;
      }
    },
    getDescProgress: function () {
      if (this.inputs.desc === '') {
        return 0
      } else {
        return ((document.querySelector('.results__content__desc--desc').offsetWidth) / 960) * 100;
      }
    },
    capitalizeTitle: function () {
      document.getElementById('metaTitle').classList.add('capitalize')
    },
    saveImg: function () {
      html2canvas(document.getElementById('canvas')).then(function (canvas) {
        saveAs(canvas.toDataURL(), 'file-name.png');
      });

      function saveAs(uri, filename) {
        let link = document.createElement('a');
        if (typeof link.download === 'string') {
          link.href = uri;
          link.download = filename;

          //Firefox requires the link to be in the body
          document.body.appendChild(link);

          //simulate click
          link.click();

          //remove the link when done
          document.body.removeChild(link);
        } else {
          window.open(uri);
        }
      }

      this.captureBtn = 'Obraz zapisano'
    },
    copy: function () {
      let title
      if (this.inputs.title === '') {
        title = this.content.title
      } else {
        title = this.inputs.title
      }
      let desc
      if (this.inputs.desc === '') {
        desc = this.content.desc
      } else {
        desc = this.inputs.desc
      }
      const copyHTML = `<title>${title}</title> <meta name="description" content="${desc}" />`

      const el = document.createElement('textarea');
      el.value = copyHTML;
      el.setAttribute('readonly', '');
      el.style = {display: 'none'};
      document.body.appendChild(el);
      el.select();
      document.execCommand('copy');
      document.body.removeChild(el);
      this.copyBtn = 'Skopiowano do schowka'
    },
    reset: function () {
      this.inputs.url = ''
      this.inputs.title = ''
      this.inputs.desc = ''
      this.inputs.bold = ''
      document.getElementById('metaTitle').classList.remove('capitalize')
      this.captureBtn = 'Pobierz zrzut ekranu'
      this.copyBtn = 'Skopiuj HTLM'
    },
    getData: function () {
      const s = this;
      $.post("http://prym.m-m.work/agregat/url.php", {url: this.inputs.url})
          .done(function (data) {
            s.response = JSON.parse(data)
          });
    },

  }

}
</script>

<style scoped>
@import "../styles/style.scss";
</style>
