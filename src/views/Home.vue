<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <shopping-list />
  </div>
</template>

<script>
// @ is an alias to /src
import ShoppingList from '@/components/ShoppingList.vue';

export default {
  name: 'Home',
  components: {
    ShoppingList,
  },
};
</script>
