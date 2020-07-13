<template>
  <div>
    <h1>お買い物リスト</h1>
    <table>
        <thead v-pre>
            <tr>
                <th class="index">No</th>
                <th class="name">商品名</th>
                <th class="memo">メモ</th>
                <th class="state">状態</th>
            </tr>
        </thead>
        <tbody>
            <tr 
              v-for="(item, key) in products"
              :key="key"
            >
              <td class="index">{{ key + 1 }}</td>
              <td class="name">{{ item.name }}</td>
              <td class="memo">{{ item.memo }}</td>
              <td class="state">{{ item.state }}</td>
            </tr>
        </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import store from './../store';
import { Component, Vue } from 'vue-property-decorator';
type Data = {
  products: [],
  productName: String,
  productMemo: String,
  current: Number,
  options: [
    {value: Number; label: String;},
    {value: Number; label: String;},
    {value: Number; label: String;},
  ],
  isEntered: Boolean
}
export default {
  data(): Data {
    return {
      products: [],
      productName: '',
      productMemo: '',
      current: -1,
      options: [
          { value: -1, label: 'すべて' },
          { value:  0, label: '未購入' },
          { value:  1, label: '購入済' }
      ],
      isEntered: false
    }
  },
  computed: {
  },
  mounted() {
    this.doFetchAllProducts()
  },
  methods: {
    doFetchAllProducts() {
      axios.get('http://localhost:8080/fetchAllProducts')
      .then(res => {
          if (res.status != 200) {
              throw new Error('レスポンスエラー')
          } else {
              let resultProducts = res.data
              this.products = resultProducts
          }
      })
    },
  }
}
</script>
<style scoped lang="scss">
.alert-color {
    border-color: #ff0000;
}
table {
    border-collapse: collapse;
    margin: 0 auto;
}
thead, tbody {
    display: block;
}
tbody {
    height: 250px;
    width: 592px;
    overflow-x: hidden;
    overflow-y: scroll;
}
th,td {
    background-color: #ffffff;
    border: 1px solid #ffcc66;
    padding: 0 8px;
    line-height: 40px;
}
thead th {
    background-color: #fff5e5;
    border: 1px solid #ffcc66;
    color: #ff9900;
}
th.index, td.index {
    width: 50px;
}
th.name, td.name {
    width: 100px;
}
th.memo, td.memo {
    width: 200px;
}
th.state, td.state {
    width: 70px;
    text-align: center;
}
th.delete, td.delete {
    width: 70px;
    text-align: center;
}
tbody tr:hover td {
    background: #ccffff;
}
</style>
