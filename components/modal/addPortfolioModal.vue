<template>
  <b-modal id="bv-modal-add-portfolio" title="作品・実績追加" hide-footer>
    <b-form @submit="addPortfolio">
      <b-form-group
        label="Title"
        label-for="title-input"
        invalid-feedback="Title is required"
      >
        <b-form-input
          id="title-input"
          v-model="newPortfolio.title"
          type="text"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group label="Show" label-for="show-input">
        <b-form-textarea
          id="show-input"
          v-model="newPortfolio.show"
          rows="3"
          type="text"
        ></b-form-textarea>
      </b-form-group>

      <b-button class="float-right" type="submit" variant="primary"
        >作品・実績追加</b-button
      >
    </b-form>
  </b-modal>
</template>

<script>
import Vue from 'vue'
import { mapActions, mapState } from 'vuex'

export default Vue.extend({
  fetch({ store }) {
    store.commit('sessionGraphicker/setSessionFromCookie')
  },
  data() {
    return {
      newPortfolio: {
        title: '',
        show: ''
      },
      graphickerId: this.$store.getters['sessionGraphicker/getId'],
      token: this.$store.getters['sessionGraphicker/getToken']
    }
  },
  computed: {
    ...mapState('sessionGraphicker', {
      graphicker: 'graphicker'
    }),
    ...mapState('portfolios', {
      portfolios: 'portfolios',
      isCreateError: 'isCreateError'
    })
  },
  mounted() {
    this.fetchGraphickerPortfolios({ graphickerId: this.graphickerId })
  },
  methods: {
    async addPortfolio(event) {
      event.preventDefault()

      await this.createPortfolios({
        title: this.newPortfolio.title,
        show: this.newPortfolio.show,
        graphickerId: this.graphickerId,
        token: this.token
      })

      // 登録失敗
      if (this.isCreateError) {
        return
      }

      await this.fetchGraphickerPortfolios({ graphickerId: this.graphickerId })

      this.$bvModal.hide('bv-modal-add-portfolio')
    },
    ...mapActions('portfolios', {
      createPortfolios: 'createPortfolios',
      fetchGraphickerPortfolios: 'fetchGraphickerPortfolios'
    })
  }
})
</script>
