# vue-infinite-spinner
![BeBXfoIu2U](https://user-images.githubusercontent.com/35951053/83335781-d4bb9700-a2b7-11ea-93c0-6f69f3243dd8.gif)

This is a simple customizable vue.js component 🚀
                              
When vue-infinite-spinner is visible in viewport, it emits event with a certain frequency ☕

Also it displays beautiful svg spinner when data is loading 😻
## How to use:
### 1. Add vue-infinite-spinner to your project
via yarn:
```sh
yarn add vue-infinite-spinner
```
via npm:
```sh
npm i vue-infinite-spinner
```
### 2. Import vue-infinite-spinner and use it as vue.js component
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
## Examples
### Spinner for tabular data:
![fVLCkfhQWN](https://user-images.githubusercontent.com/35951053/83334718-0f6e0100-a2b1-11ea-8f3c-0b141c25daf7.gif)
```vue
<template>
    <div class="commit-history">
        <table class="table table-bordered table-sm table-responsive">
            <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Author</th>
                    <th scope="col">Date</th>
                    <th scope="col">Commit message</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(commitObj, commitIndex) in fetchedCommitsArray" :key="commitIndex">
                    <th scope="row">{{ commitIndex + 1 }}</th>
                    <th>{{ commitObj.commit.author.name }}</th>
                    <th>{{ commitObj.commit.author.date | formattedDate }}</th>
                    <th>{{ commitObj.commit.message }}</th>
                </tr>
            </tbody>
        </table>

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
    import axios from 'axios';

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
            * Fetch some data if it is not loading at the moment and it hasn't loaded yet
            */
            async fetchDataWhenSpinnerVisible() {
                if (!this.isLoading && !this.isLoadingDone) {
                    this.isLoading = true;
                    const url = `https://api.github.com/repos/vuejs/vue-class-component/commits?page=${this.page}&per_page=${this.perPage}`;
                    axios.get(url, {
                        method: 'GET'
                    }).then(response => {
                        if (response.status === 200 && Array.isArray(response.data)) {
                            if (response.data.length) {
                                this.fetchedCommitsArray.push(...response.data);
                                this.page++;
                            } else {
                                // loading is done if current page is the last page
                                this.isLoadingDone = true;
                            }
                        }
                    }).finally(() => {
                        this.isLoading = false;
                    });
                }
            }
        }
    }
</script>
```
## Documentation
### Props
| Name | Type | Default | Required | Description |
| :--- | :--- | :--- | :--- | :--- |
| `isLoadingDone` | Boolean | false |  | Was loading competed? |
| `isNotFound` | Boolean | false |  | No items found? |
| `message` | String | 'Loading' |  | Text message displaying when property `isNotFound` = false |
| `messageNotFound` | String | 'No items found' |  | Text message displaying when property `isNotFound` = true and property `isLoadindDone` = true |
| `horizontalAlign` | String | 'left' |  | ['left', 'right', 'center'] |
| `spinnerStyle` | Object | {} |  | Style for default svg-spinner element. *For example: `:spinnerStyle="{width: '32px', height: '32px'}"`* |
| `spinnerColor` | String | '#E30611' |  | HEX-color default svg-spinner element |
| `interval` | Number | 500 |  | Interval to repeat check of visibility vue-infinite-spinner in browser viewport |
| `visibleEventName` | String | 'visible' |  | Event name, which emits when vue-infinite-spinner is visible |
| `scrollElementId` | String | *window* |  | Id of viewport html-element |
| `mini` | Boolean | false |  | Show mini version? |
### Slots
| Name | Description |
| :--- | :--- |
| `spinner` | Svg-element which is spinning. *NOTE: if you want to provide your own spinner, you should care about animation of your spinner by yourself. If you just want to configurate animation of default spinner, you should use for your purposes property `spinnerStyle`*  |
| `message` | Element with message which displays when data is loading. *NOTE: default message slot contains pretty animation 😻 with three dots after message text. So if you provide your own message slot such effect will be lost 😿* |
| `not-found` | Element with message which displays when empty data has been loaded (when flags `isLoadingDone` and `isNotFound` is true) |
### Events
| Name | Description |
| :--- | :--- |
| `visible` | It emits after each `interval` time, when vue-infinite scroll is visible in browser viewport. *NOTE: you can customize interval and event name using props `interval` and `scrollElementId`* |
