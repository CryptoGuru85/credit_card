<template>
  <div>
    <CreditCard/>
  </div>
</template>
<script>
import CreditCard from './components/CreditCard.vue'
export default {
  components: {
    CreditCard
  },
  /**
   * Chart data used to render stats, charts. Should be replaced with server data
   */
  data() {
    return {
    };
  }
};
</script>
