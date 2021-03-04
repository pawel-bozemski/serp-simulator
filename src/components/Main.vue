<template>
  <div class="main row">
    <div class="col-5 inputs">
      <div class="inputs__input">
        <label for="url" class="inputs__input__title">
          URL
        </label>
        <input id="url" type="text" placeholder="e.g. mangools.com" v-model="inputs.url">
        <br>
        <button type="button" class="btn btn-light btn-sm url-btn" @click="fetchMeta()">meta</button>

      </div>
      <div class="inputs__input">
        <label for="title" class="inputs__input__title">
          Title
        </label>
        <p class="inputs__input__count">{{ inputs.title.length }} chars (<span
            v-bind:class="[getTitleWidth() > 600 ? 'error' : '']">
          {{ getTitleWidth() }}
        </span>/600px)</p>
        <b-progress class="mt-2" :max="100">
          <b-progress-bar :value="(getTitleProgress())"
                          :variant="getTitleProgress() > 100 ? 'danger' : 'success'"></b-progress-bar>
        </b-progress>
        <input id="title" type="text" placeholder="Try to keep the title under 600px to displey it in full length"
               v-model="inputs.title">
        <div v-if="getTitleWidth() > 600" class="inputs__input__error">
          The title is wider than 600px and it may not be displayed in full length.
        </div>

        <br>
        <button type="button" class="btn btn-light btn-sm" @click="capitalizeTitle">Capitalize title</button>
      </div>
      <div class="inputs__input">
        <label for="desc" class="inputs__input__title">
          Description
        </label>
        <p class="inputs__input__count">{{ inputs.desc.length }} chars (<span
            v-bind:class="[getDecsWidth() > 960 ? 'error' : '']">
          {{ getDecsWidth() }}
        </span>/960px)</p>
        <b-progress class="mt-2" :max="100">
          <b-progress-bar :value="(getDescProgress())"
                          :variant="getDescProgress() > 100 ? 'danger' : 'success'"></b-progress-bar>
        </b-progress>
        <textarea id="desc"
                  placeholder="The meta description may get trimmed at ~960 pixels on desktop and at ~680px on mobile.
                  Keep it below ~158 chars."
                  v-model="inputs.desc"> </textarea>
        <div class="inputs__input__error" v-if="getDecsWidth() > 960">
          The meta description may get trimmed at ~960 pixels on desktop and at ~680px on mobile. Keep it below ~158
          characters.
        </div>
      </div>
      <div class="inputs__input">
        <label for="bold" class="inputs__input__title">
          Bold keywords
        </label>
        <input id="bold" type="text" placeholder="Separate with space" v-model="inputs.bold">
        <br>
        <button type="button" class="btn btn-info btn-sm" @click="findBolds()">Bold keywords</button>
      </div>
      <div class="inputs__input--checkbox">
        <p class="inputs__input__title">
          Options
        </p>
        <label class="inputs__input--checkbox" for="date">
          <input id="date" type="checkbox" @click="showDate()"> Date
        </label>
        <label class="inputs__input--checkbox" for="rating">
          <input id="rating" type="checkbox" @click="showStars()"> Rating
        </label>
        <label class="inputs__input--checkbox" for="ads">
          <input id="ads" type="checkbox" @click="showAds()"> Ads
        </label>
        <label class="inputs__input--checkbox" for="map">
          <input id="map" type="checkbox" @click="showMap()"> Map pack
        </label>
      </div>
      <div class="inputs__input--buttons">
        <button class="btn btn-light btn-sm" @click="reset()">Reset</button>
        <button class="btn btn-primary btn-sm" @click="saveImg()">{{ captureBtn }}</button>
        <button class="btn btn-primary btn-sm" @click="copy()"> {{ copyBtn }}</button>
      </div>
    </div>
    <div id="canvas" class="col-7 results">
      <div class="separator"></div>
      <div class="results__header">
        <div class="row">
          <div class="col-2 results__header__logo">
            <img src="../assets/img/logo.png" alt="">
          </div>
          <div class="col-10 results__header__main">
            <div class="col-12 results__header__main__input">
              <div class="input-group">
                <input type="text" disabled class="form-control"
                       placeholder="Enter a keyword to get real search results" aria-label="Username"
                       aria-describedby="basic-addon1">
              </div>
            </div>
            <div class="separator"></div>
            <div class="col-12 results__header__main__menu">
              <div class="results__header__main__menu">
                <div class="row">
                  <div v-for="item in menuItems" :key="item.message"
                       class="results__header__main__menu__item">
                    <p class="results__header__main__menu__item-item">
                      {{ item.message }}
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
              About 608,000,000 results (0.54 seconds)
            </p>
            <div class="results__content__ads" v-bind:class="[{ 'show' : isAd}]">
              <div class="results__content__url results__content__url--ad">
                mangools.com
              </div>
              <div class="results__content__title">
                Mangools: Juicy SEO Tools You Will Love
              </div>
              <div class="results__content__desc">
                Mangools is a bundle of 5 simple but powerful SEO tools made for an effective SEO workflow.
                Loved by beginners, trusted by professionals. Try it for free!
              </div>
            </div>
            <div class="results__content__ads" v-bind:class="[{ 'show' : isAd}]">
              <div class="results__content__url results__content__url--ad">
                mangools.com
              </div>
              <div class="results__content__title">
                Mangools: Juicy SEO Tools You Will Love
              </div>
              <div class="results__content__desc">
                Mangools is a bundle of 5 simple but powerful SEO tools made for an effective SEO workflow.
                Loved by beginners, trusted by professionals. Try it for free!
              </div>
            </div>
            <img v-bind:class="[{ 'show' : isMap}]" width="600" class="results__content__map"
                 src="../assets/img/map.png" alt="">
            <div class="results__content__metadata">
              <div class="results__content__url results__content__url--metadata">
                <p v-if="inputs.url === ''">{{ showUrl() }}</p>
                <p v-else>{{ inputUrl() }}</p>
              </div>
              <div id="metaTitle" class="results__content__title results__content__url--metadata">
                <p v-if="inputs.title === ''">{{ content.title }}</p>
                <p v-else>{{ inputs.title }}</p>
              </div>

              <div class="results__content__desc results__content__url--metadata" id="metaDesc">
                <span class="results__content__desc--date"
                      v-bind:class="[{ 'show' : isDate}]">Lut 20,2021 - </span>
                <span v-if="inputs.desc === ''">{{ content.desc }}</span>
                <span v-else v-html="inputs.desc"></span>
                <br>
                <div class="results__content__stars" v-bind:class="[{ 'show' : isStar}]">
                  <img src="../assets/img/stars.png" alt="">
                  Rating 5/5 - 112 votes
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

export default {
  data() {
    return {
      menuItems: [
        {message: 'All'},
        {message: 'Images'},
        {message: 'Videos'},
        {message: 'Maps'},
        {message: 'More'}
      ],
      content: {
        url: 'example/url',
        title: 'This is an example title',
        desc: 'Here is an example of what a snippet looks like in Googles SERPs. The content that appears there is usually taken from meta description tag if relevant.'
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
      copyBtn: 'Export HTML',
      captureBtn: 'Capture'
    }
  },
  methods: {
    showUrl: function () {
      return this.content.url.replace('/', ' > ');
    },
    inputUrl: function () {
      return this.inputs.url.replace('/', ' > ');
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
        return document.getElementById('metaTitle').offsetWidth;
      }
    },
    getTitleProgress: function () {
      if (this.inputs.title === '') {
        return 0
      } else {
        return ((document.getElementById('metaTitle').offsetWidth) / 600) * 100;
      }
    },
    getDecsWidth: function () {
      if (this.inputs.desc === '') {
        return 0
      } else {
        return document.getElementById('metaDesc').offsetWidth;
      }
    },
    getDescProgress: function () {
      if (this.inputs.desc === '') {
        return 0
      } else {
        return ((document.getElementById('metaDesc').offsetWidth) / 960) * 100;
      }
    },
    capitalizeTitle: function () {
      document.getElementById('metaTitle').classList.add('capitalize')
    },
    findBolds: function () {
      let string = this.inputs.desc
      const boldStr = this.inputs.bold
      let separatedStr = boldStr.split(' ');
      separatedStr.forEach(function (bold) {
        if (string.includes(bold)) {
          string = string.replace(bold, `<strong>${bold}</strong>`)
        }
      })
      this.inputs.desc = string
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
      this.captureBtn = 'Image Saved!'
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
      this.copyBtn = 'Copied to clipboard'
    },
    reset: function () {
      this.inputs.url = ''
      this.inputs.title = ''
      this.inputs.desc = ''
      this.inputs.bold = ''
      document.getElementById('metaTitle').classList.remove('capitalize')
      this.captureBtn = 'Capture'
      this.copyBtn = 'Export HTML'
    }
  }


}
</script>

<style scoped>
@import "../styles/style.scss";
</style>
