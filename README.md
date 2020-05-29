# vue-infinite-spinner

This is a simple customizable vue.js component 🚀
                                             
When vue-infinite-spinner is visible in viewport, it emits event with a certain frequency ☕

Also it displays beautiful svg spinner when data is loading 😻

## Add vue-infinite-spinner to your project
via yarn:
```sh
yarn add vue-infinite-scroll
```

via npm:
```sh
npm i vue-infinite-spinner
```

## Import vue-infinite-spinner and use it
```vue
<template>
  <VueInfiniteSpinner/>
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

## Example
```vue
<template>
  <div class="commit-history">
      <ol class="commit-history__list">
        <li v-for="(commit, commitIndex) in fetchedCommitsArray" class="commit-history__elem" :key="commitIndex">
          date: {{ commit.author.date }} | author: {{ commit.author.name }} | message: {{ commit.message }}
        </li>
      </ol>
      <VueInfiniteSpinner
          message="Next page is loading, please wait"
          messageNotFound="No commits was found for your request 😿"
          :isLoadingDone="isLoadingDone"
          :isNotFound="!fetchedCommitsArray.length"
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
        },

        data() {
            return {
                page: 1,
                perPage: 30,
                isLoading: false,
                isLoadingDone: false,
                fetchedCommitsArray: []
            }   
        },

        methods: {
           /**
            * Fetch some data if it is not loading at the moment it hasn't loaded yet
            */
            async fetchDataWhenSpinnerVisible() {
                if (!this.isLoading && !this.isLoadingDone) {
                    this.isLoading = true;
                    const url = `https://api.github.com/repos/SeminVsevolod/vue-infinite-spinner/commits?page${page}&per_page${perPage}`;
                    const response = await fetch(url);
                    this.fetchedCommitsArray = await response.json();
                    if (response.ok) {
                      this.isLoadingDone = true;
                      this.page++;
                      const linkHeader = response.headers.get('link');
                      const linkHeaderArray = linkHeader.split(',');
                      if (linkHeaderArray.some(header => header.includes(`page=${this.page}` && header.includes('rel="last"')))) {
                        // loading is done if current page is the last page
                        this.isLoadingDone = true;                  
                      }
                    }
                }  
            }     
        }
    }
</script>
```

