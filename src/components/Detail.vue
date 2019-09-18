<template>
  <div class="detail-container">
    <div class="text-right">
      <button class="bg-transparent border-0" @click.prevent="closeAlert"><span
        aria-hidden="true">&times;</span></button>
    </div>
    <h4 class="m-3">{{title}}</h4>
    <p class="text-right text-muted mb-2 border-top border-bottom">
      Sum: {{data.sum | formatNumber}}</p>
    <div class="p-3" v-for="key in infoKeys" :key="key">
      <!--  nested properties visualizer  -->
      <div v-if="hasProperties(key)">
        <h6 class="text-capitalize text-left" v-if="data[key]">{{key | filterKeyname}}</h6>
        <table class="table table-bordered text-left" v-if="key !== 'other'">
          <tr v-for="detail in Object.keys(data[key])" :key="detail">
            <td class="text-capitalize">{{detail | filterKeyname}}</td>
            <td v-if="detail !== 'other'"> {{data[key][detail] | formatNumber}}</td>
            <td v-else-if="data[key][detail].length">
              <ul>
                <li class="text-left" v-for="other in data[key][detail]" :key="other.name">
                  <p class="p-0 m-0">Name:
                    <span class="font-weight-bold text-break">{{other.name}}</span></p>
                  <p class="p-0 m-0">Amount:
                    <span class="font-weight-bold">{{other.amount | formatNumber}}</span></p>
                </li>
              </ul>
            </td>
            <td v-else>0</td>
          </tr>
        </table>
        <table class="table text-left" v-else>
          <thead>
          <tr>
            <td>Name</td>
            <td>Amount</td>
          </tr>
          </thead>
          <tbody>
          <tr v-for="other in data[key]" :key="other.name">
            <td>{{other.name}}</td>
            <td class="font-weight-bold">{{other.amount | formatNumber}}</td>
          </tr>
          </tbody>

        </table>
      </div>
      <div class="d-flex justify-content-between text-capitalize" v-else>
        <span>{{key | filterKeyname}}</span>
        <span class="font-weight-bold" v-if="!data[key].length">0</span>
        <!--   table of other nested information   -->
        <table v-else>
          <thead>
          <tr>
            <td>Name</td>
            <td>Amount</td>
          </tr>
          </thead>
          <tbody>
          <tr v-for="other in data[key]" :key="other.name">
            <td>{{other.name}}</td>
            <td class="font-weight-bold">{{other.amount | formatNumber}}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Detail',
  props: {
    data: Object,
    title: String,
  },
  data() {
    return {};
  },
  filters: {
    filterKeyname(key) {
      return key.replace(/_/g, ' ');
    },
    formatNumber(number) {
      let formatted;
      formatted = Math.round(number * 100) / 100;
      formatted = formatted.toString()
        .replace('.', ',')
        .replace(/\B(?=(\d{3})+(?!\d))/g, '.');
      return formatted;
    },
  },
  computed: {
    infoKeys() {
      return Object.keys(this.data)
        .filter(key => key !== 'sum');
    },
  },
  methods: {
    hasProperties(key) {
      const properties = Object.keys(this.data[key]);
      return properties.length > 0;
    },
    closeAlert() {
      this.$emit('hide-detail', false);
    },
  },
};
</script>

<style scoped lang="scss">
  .detail-container {
    background-color: #fff;
    border: 1px solid #e1e1e1;
    height: 100%;
    padding: 1.5rem;
    position: fixed;
    right: 0;
    top: 0;
    width: 40%;
    z-index: 1042;
    overflow: auto;
  }
</style>
