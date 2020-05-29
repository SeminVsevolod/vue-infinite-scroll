# vue-infinite-spinner

This is a simple customizable vue.js component 🚀
                                             
When vue-infinite-spinner is visible in viewport, it emits event with a certain frequency ☕

Also it displays beautiful svg spinner when data is loading 😻

## Add vue-infinite-spinner to your project
Using yarn:
```sh
yarn add vue-infinite-scroll
```

Using npm:
```sh
npm i vue-infinite-spinner
```

## Import vue-infinite-spinner and use it
```vue
<template>
  <div class="your-awesome-component">
      <VueInfiniteSpinner
          message="Loading, please wait"
          messageNotFound="No information was found for your request 😿"
          :isLoadingDone="isLoadingDone"
          :isNotFound="!fetchedArray.length"
          spinnerColor="red"
          horizontalAlign="left" 
          @visible="fetchDataWhenSpinnerVisible">
      </VueInfiniteSpinner>
  </div>
</template>

<script> 
    import VueInfiniteSpinner from "vue-infinite-spinner";
    
    export default {
        components: {
            VueInfiniteSpinner
        }
    }
</script>
```

