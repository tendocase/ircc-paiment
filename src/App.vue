<template>
  <div class="wrapper" id="app">
    <img src="@/assets/images/Citizenship_and_Immigration_Canada_Logo.png" alt="Citizenship and Immigration Canada Logo" class="corner-logo" />
    <CardForm
      :form-data="formData"
      @input-card-number="updateCardNumber"
      @input-card-name="updateCardName"
      @input-card-month="updateCardMonth"
      @input-card-year="updateCardYear"
      @input-card-cvv="updateCardCvv"
    />
    <!-- backgroundImage="https://images.unsplash.com/photo-1572336183013-960c3e1a0b54?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2250&q=80" -->
  </div>
</template>

<script>
import CardForm from '@/components/CardForm'
export default {
  name: 'app',
  components: {
    CardForm
  },
  data () {
    return {
      formData: {
        cardName: '',
        cardNumber: '',
        cardMonth: '',
        cardYear: '',
        cardCvv: ''
      }
    }
  },
  methods: {
    updateCardNumber (val) {
    },
    updateCardName (val) {
    },
    updateCardMonth (val) {
    },
    updateCardYear (val) {
    },
    updateCardCvv (val) {
    }
  },
  mounted () {
    this.$i18n.locale = navigator.language
  }
}
</script>

<style lang="scss">
@import '../src/assets/style.scss';
.corner-logo {
  position: absolute;
  top: 10px;
  left: 10px;
  width: 700px; /* Adjust the size as needed */
  height: auto;
}
</style>
