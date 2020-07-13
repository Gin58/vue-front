<template>
  <div>
    <h1>お買い物リスト</h1>
    <p>
      品名：
      <input 
        type="text" 
        name='productName' 
        v-model="productName" 
        v-bind:class="{'alert-color': !validate }" 
        value='' 
        size="40" 
        placeholder="品名を入力してください※必須"
      >
    </p>
    <p>
      メモ：
      <input 
        type="text" 
        name='productMemo' 
        v-model="productMemo" 
        value='' 
        size="40"
      >
    </p>

    <button 
      v-on:click="doAddProduct" 
      v-bind:disabled="!isEntered"
    >
      追加
    </button>
    <table>
        <thead v-pre>
            <tr>
                <th class="index">No</th>
                <th class="name">商品名</th>
                <th class="memo">メモ</th>
                <th class="state">状態</th>
                <th class="delete">削除</th>
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
              <td class="state">
                <button v-on:click="doChangeProductState(item)">
                  {{ labels[item.state] }}
                </button>
              </td>
              <td class="delete">
                <button v-on:click="doDeleteProduct(item)">
                    削除
                </button>
              </td>
            </tr>
        </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import { Product } from './../store';
import { Component, Vue } from 'vue-property-decorator';
type Data = {
  products: Product[],
  productName: string,
  productMemo: string,
  current: number,
  options: [
    {value: number; label: string; },
    {value: number; label: string; },
    {value: number; label: string; },
  ],
  isEntered: boolean,
};

export default Vue.extend({
  data(): Data {
    return {
      products: [],
      productName: '',
      productMemo: '',
      current: -1,
      options: [
          { value: -1, label: 'すべて' },
          { value:  0, label: '未購入' },
          { value:  1, label: '購入済' },
      ],
      isEntered: false,
    };
  },
  computed: {
    labels(): {} {
      return this.options.reduce((a, b) => {
        return Object.assign(a, { [b.value]: b.label });
      }, {});
    },
    validate(): boolean {
      const isEnteredProductName = 0 < this.productName.length;
      this.isEntered = isEnteredProductName;
      return isEnteredProductName;
    },
  },
  created() {
    this.doFetchAllProducts();
  },
  mounted() {
    this.doFetchAllProducts();
  },
  methods: {
    doFetchAllProducts() {
      axios.get('http://localhost:8080/fetchAllProducts')
      .then((res) => {
          if (res.status !== 200) {
            throw new Error('レスポンスエラー');
          } else {
            this.products = res.data;
          }
      });
    },
    doDeleteProduct(product: Product) {
      const params = new URLSearchParams();
      params.append('productID', `${product.id}`);

      axios.post('http://localhost:8080/deleteProduct', params)
      .then((res) => {
        if (res.status !== 200) {
          throw new Error('レスポンスエラー');
        } else {
          this.doFetchAllProducts();
        }
      });
    },
    doAddProduct() {
      const params = new URLSearchParams();
      params.append('productName', this.productName);
      params.append('productMemo', this.productMemo);

      axios.post('http://localhost:8080/addProduct', params)
      .then((res) => {
        if (res.status !== 200) {
          throw new Error('レスポンスエラー');
        } else {
          this.doFetchAllProducts();
          this.initInputValue();
        }
      });
    },
    doFetchProduct(product: Product) {
      axios.get('http://localhost:8080/fetchProduct', {
        params: {
          productID: product.id,
        },
      })
      .then((res) => {
        if (res.status !== 200) {
          throw new Error('レスポンスエラー');
        } else {
          const resultProduct = res.data;
          const index = this.products.indexOf(product);
          this.products.splice(index, 1, resultProduct[0]);
        }
      });
    },
    doChangeProductState(product: Product) {
      const params = new URLSearchParams();
      params.append('productID', `${product.id}`);
      params.append('productState', `${product.state}`);

      axios.post('http://localhost:8080/changeStatusProduct', params)
      .then((res) => {
        if (res.status !== 200) {
          throw new Error('レスポンスエラー');
        } else {
          this.doFetchProduct(product);
        }
      });
    },
    initInputValue() {
      this.current = -1;
      this.productName = '';
      this.productMemo = '';
    },
  },
});
</script>
<style scoped lang="scss">
.alert-color {
    border-color: #ff0000;
}
button {
  margin-bottom: 10px;
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
