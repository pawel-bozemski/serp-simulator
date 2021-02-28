<template>
  <div class="main row">
    <div class="col-5 inputs">
      <div class="inputs__input">
        <label for="url" class="inputs__input__title">
          URL
        </label>
        <input id="url" type="text" placeholder="e.g. mangools.com">
      </div>
      <div class="inputs__input">
        <label for="title" class="inputs__input__title">
          Title
        </label>
        <p class="inputs__input__count" >0 chars (0/600px)</p>
        <input id="title" type="text" placeholder="e.g. mangools.com">
      </div>
      <div class="inputs__input">
        <label for="desc" class="inputs__input__title">
          Description
        </label>
        <p class="inputs__input__count" >0 chars (0/600px)</p>
        <input id="desc" type="text" placeholder="e.g. mangools.com">
      </div>
      <div class="inputs__input">
        <label for="bold" class="inputs__input__title">
          Bold keywords
        </label>
        <input id="bold" type="text" placeholder="e.g. mangools.com">
      </div>
      <div class="inputs__input--checkbox">
        <p class="inputs__input__title">
          Options
        </p>
        <label class="inputs__input--checkbox" for="date">
          <input id="date" type="checkbox"> Date
        </label>
        <label class="inputs__input--checkbox" for="rating">
          <input id="rating" type="checkbox"> Rating
        </label>
        <label class="inputs__input--checkbox" for="ads">
          <input id="ads" type="checkbox"> Ads
        </label>
        <label class="inputs__input--checkbox" for="map">
          <input id="map" type="checkbox"> Map pack
        </label>
      </div>
    </div>
    <div class="col-7 results">
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
            <div class="results__content__metadata">
              <p class="results__content__metadata__search">
                About 608,000,000 results (0.54 seconds)
              </p>
              <img width="600" class="results__content__metadata__map" src="../assets/img/map.png" alt="">
              <p class="results__content__metadata__url">
                {{ showUrl() }}
              </p>
              <p class="results__content__metadata__title">
                {{ content.title }}
              </p>
              <p class="results__content__metadata__desc">
                <span class="results__content__metadata__desc--date">Lut 20,2021 - </span> {{ content.desc }}
              </p>
              <div class="results__content__metadata__stars">
                <img src="../assets/img/stars.png" alt="">
                Rating 5/5 - 112 votes
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
  </div>
</template>

<script>
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
      }
    }
  },
  methods: {
    showUrl: function () {
      return this.content.url.replace('/', ' > ');
    }
  }


}
</script>

<style scoped>
@import "../styles/style.scss";
</style>
