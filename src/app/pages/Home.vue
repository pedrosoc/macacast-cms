<template>
  <div class="home">

    <app-header></app-header>

    <section class="hero">
      <div class="background-image" :style="{'background-image': `url(${require('@/app/assets/img/hero.jpg')})`}"></div>
      <div class="hero-content">
        <h1>
          {{settings.title}}
        </h1>
        <h3>{{settings.description}}</h3>
        <div class="cta">
          <a href="https://github.com/tamiat/tamiat" class="btn is-large has-icon" target="_blank">
            Download it Here
            <i class="fa fa-arrow-circle-down"></i>
          </a>
        </div>
      </div>
    </section>

    <section-work></section-work>

    <section-service></section-service>

    <section-review></section-review>

    <app-footer></app-footer>
  </div>
</template>

<script>
import { settingsRef } from '@/admin/firebase_config'
import appHeader from '@/app/components/appHeader'
import appFooter from '@/app/components/appFooter'
import sectionWork from '@/app/components/sectionWork'
import sectionService from '@/app/components/sectionService'
import sectionReview from '@/app/components/sectionReview'
import contentFetch from '@/admin/mixins/contentFetch'

export default {
  name: 'home',
  mixins: [contentFetch],
  components: {
    appHeader,
    sectionWork,
    sectionService,
    sectionReview,
    appFooter
  },
  beforeRouteEnter (to, from, next) {
      next('/admin')
  },
  firebase: {
    // load settings as object instead of array (default)
    settings: {
      source: settingsRef,
      asObject: true
    }
  }
}

</script>

<style lang="scss" scoped>
@import '@/app/styles/index.scss';

/*----------------
       Hero Section
  ----------------*/

.hero {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  justify-content: center !important;
  min-height: 100vh;
  color: #fff;
  text-align: center;
  z-index: 0;
  .background-image {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-size: cover;
    background-color: #2196f3;
    z-index: -1;
    &:after {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: #414a4f;
      opacity: 0.75;
    }
  }
  .hero-content {
    position: absolute;
    right: 20%;
    text-align: left;
    h1 {
      font-size: 32px;
      margin-bottom: 15px;
    }

    h3 {
      font-size: 25px;
      margin-bottom: 20px;
    }

    .cta {
      padding-top: 15px;
    }
  }
}

@media (max-width: 800px) {
  .hero {
    min-height: 600px;
    h1 {
      font-size: 48px;
    }
    h3 {
      font-size: 24px;
    }
    a.btn {
      padding: 15px 40px;
    }
  }
}

@media (max-width: 600px) {
  .hero .hero-content {
    padding: 0 30px;
    right: auto;
    text-align: center;
  }
}
</style>
