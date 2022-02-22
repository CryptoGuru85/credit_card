<template>
  <div>
    <fieldset class="card-container">
      <legend>Credit Card Information</legend>
      <div class="card-number">
        <img :src="ccType ? require('../assets/' + ccType + '.png') : require('../assets/front.png')" alt="card">
        <input v-if="inputAlert" type="text" :value="formatCardNumber.substr(15)" @input="handleChange" :class="ccType?'slide-fade-enter-active':''" name="card" id="card" pattern="[0-9]*" placeholder="xxxx xxxx xxxx xxxx" class="effect">
        <input v-else type="text" :value="formatCardNumber" @input="handleChange" name="card" id="card" pattern="[0-9]*" placeholder="xxxx xxxx xxxx xxxx" class="effect">
        <input v-if="inputAlert" :value="formatExpiration" @input="handleExpiration" placeholder="MM/YY"  maxlength="5" name="expiration" pattern="[0-9]*" type="text" class="card-expiration" id="expiration">
        <input v-if="inputAlert" :value="ccv" @input="handleCvv" placeholder="CVV" @keypress="imageChange" maxlength="3" pattern="[0-9]*" type="text" class="card-cvv" id="card-cvv">
        <input v-if="inputAlert" :value="zip" @input="handleZip" placeholder="ZIP" maxlength="5" pattern="[0-9]*" type="text" class="card-zip" id="card-zip">
      </div>
    </fieldset>
    <div v-if="!successAlert">
      <div v-if="!ccType">
        <div v-if="!wrongNumberAlert">Please enter your credit card number</div>
        <div v-else class="errorColor">Please enter a valid credit card number</div>
      </div>
      <div v-else>Please enter your card's expiration month and year</div>
    </div>
    <div v-else class="success">Hooray! You've successfully filled out your credit card information.</div>
  </div>
</template>
<script>
const ccDefinitions = {
			'visa': /^4/,
			'mc': /^5[1-5]/,
			'amex': /^3(4|7)/,
			'disc': /^6011/
		};
export default {
  components: {

  },
  data: () => ({
    cardNumber: '',
    expiration: '',
    ccv: '',
    zip: '',
    ccType : '',
    wrongNumberAlert: false,
    successAlert: false,
    inputAlert: false,
  }),
  computed: {
    formatCardNumber() {
      return this.cc_format(this.cardNumber).toString()
    },
    formatExpiration() {
      return this.ce_format(this.expiration).toString()
    },
  },
  methods: {
    cc_format(value) {
      let v = value.replace(/\s+/g, '').replace(/[^0-9]/gi, '')
      let matches = v.match(/\d{4,16}/g);
      let match = matches && matches[0] || ''
      let parts = []

      for (let i=0, len=match.length; i<len; i+=4) {
          parts.push(match.substring(i, i+4))
      }

      if (parts.length) {
          return parts.join(' ')
      } else {
          return value
      }
    },

    ce_format(item) {

      item = item.split('/').join('');    // Remove slash (/) if mistakenly entered.
      if(item.length < 6 && item.length > 0){
          let finalVal = item.match(/.{1,2}/g).join('/');
        return finalVal
      }
      return item
    },
    handleChange(e) {
      this.cardNumber = e.target.value
      console.log("this.cardNumber.length ", this.cardNumber.length )
      console.log("this.getCreditCardType(this.cardNumber)", this.getCreditCardType(this.cardNumber))
      if(this.cardNumber.length == 19 && !this.getCreditCardType(this.cardNumber))
      {
        this.wrongNumberAlert = true
      }
      if(this.cardNumber.length == 19 && this.getCreditCardType(this.cardNumber))
      {
        this.inputAlert = true
        setTimeout(() => {this.getFocus("expiration")}, 100);
      }
    },
    handleExpiration(e) {
      this.expiration = e.target.value
      if(this.expiration.length == 5)
      {
        this.getFocus("card-cvv")
      }
    },
    handleCvv(e) {
      console.log("e.target.value", e.target.value)
      this.ccv = e.target.value
      if(this.ccv.length == 3)
      {
        this.getFocus("card-zip")
        console.log("this.ccv", this.ccv)
      }
    },
    handleZip(e) {
      this.zip = e.target.value
      console.log("this.zip", this.zip)
      if(this.zip.length == 5)
      {
        this.successAlert = true
      }
    },
    getFocus(id) {
      console.log("hello", id)
      document.getElementById(id).focus();
    },
    getCreditCardType (number) {
      for ( const [key, value] of Object.entries(ccDefinitions)) {
        console.log("value.test(number)",value.test(number))
        if (value.test(number)) {
          this.ccType = key;
          return this.ccType;
        }
        return false;
      }
    },
    imageChange() {
      this.ccType = "back"
    }
  }
};
</script>
<style scoped>
.card-container {
  position: relative;
  display: flex;
  width: 100%;
  flex-direction: column;
  margin: 5px 0;
}
input[type="text"] {
  display: block;
  margin: 10px 0;
  font-size: 20px;
  width: 200px;
  border: none;
}
input[type="text"]:focus {
   outline: none;
}
/* legend {
  position: absolute;
  top: -2px;
  left: 10px;
  font-size: 20px;
  background-color: white;
} */
img {
  width: 60px;
  height: 30px;
}

input::placeholder {
  color: gray;
}
.card-number {
  display: flex;
  align-items: center;
}
.errorColor {
  color : red;
}
.success {
  color: green
}

</style>
