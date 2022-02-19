<template>
  <form>
    <fieldset class="card-container">
      <legend>Credit Card Information</legend>
      <div class="card-number">
        <img :src="ccType ? require('../assets/' + ccType + '.png') : require('../assets/front.png')" alt="card">
        <input type="text" :value="formatCardNumber" @input="handleChange" name="card" id="card" pattern="[0-9]*" placeholder="xxxx xxxx xxxx xxxx" class="effect">
        <input v-if="ccType" :value="formatExpiration" @input="handleExpiration" placeholder="MM/YY"  maxlength="5" pattern="[0-9]*" type="text" class="card-expiration" id="card-expiration">
        <input v-if="ccType" placeholder="CVV" maxlength="3"  pattern="[0-9]*" type="text" class="card-cvv" id="card-cvv">
        <input v-if="ccType" placeholder="ZIP" maxlength="5" pattern="[0-9]*" type="text" class="card-zip" id="card-zip">
      </div>
    </fieldset>
    <div>Please enter your credit card number</div>
  </form>
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

  data() {
    return {
      cardNumber: '',
      expiration: '',
      ccv: '',
      zip: '',
      ccType : ''
    };
  },
  computed: {
    formatCardNumber() {
      return this.cc_format(this.cardNumber).toString()
    },
    formatExpiration() {
      return this.ce_format(this.expiration).toString()
    }
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
      console.log("hello", this.cardNumber)
      console.log(this.getCreditCardType(this.cardNumber))
    },
    handleExpiration(e) {
      this.expiration = e.target.value
    },
    getCreditCardType (number) {
      for ( const [key, value] of Object.entries(ccDefinitions)) {
        console.log("value.test(number)",value.test(number))
        if (value.test(number)) {
          this.ccType = key;
          return this.ccType;
        }
      }
    },
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
</style>
