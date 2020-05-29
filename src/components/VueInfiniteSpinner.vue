<template>
  <div class="vue-infinite-spinner" :class="'vue-infinite-spinner_' + horizontalAlign">
    <template v-if="!isLoadingDone">
      <slot name="spinner">
        <svg class="vue-infinite-spinner__spinner" :style="spinnerStyle" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
          <g fill="none" fill-rule="evenodd">
            <path d="M0 0h24v24H0z"/>
            <path :stroke="spinnerColor" stroke-width="2" d="M12 3a9 9 0 1 0 6.3 2.573"/>
          </g>
        </svg>
      </slot>

      <slot name="message">
        <span class="vue-infinite-spinner__message"
              :class="{'vue-infinite-spinner__message_mini': mini}">
          {{ message }} <span>.</span> <span>.</span> <span>.</span>
        </span>
      </slot>
    </template>

    <slot name="not-found" v-if="isLoadingDone && isNotFound">
      <span class="vue-infinite-spinner__message"
            :class="{'vue-infinite-spinner__message_mini': mini}">
        {{ messageNotFound }}
      </span>
    </slot>
  </div>
</template>

<script>
  export default {
    name: 'VueInfiniteSpinner',

    props: {
      /**
       * Сообщение, которое выводится при загрузке
       */
      message: {
        type: String, default: 'Loading'
      },

      /**
       * Сообщение, которое выводится если передан флаг "не найдено" (isNotFound = true)
       */
      messageNotFound: {
        type: String, default: 'Data not found'
      },

      /**
       * Стиль для спинера
       */
      spinnerStyle: {
        type: Object, default: () => {}
      },

      /**
       * Цвет спинера
       */
      spinnerColor: {
        type: String, default: '#E30611'
      },

      /**
       * Загрузка завершена?
       */
      isLoadingDone: {
        type: Boolean, default: false
      },

      /**
       * Не найдено элементов по заданным параметрам?
       */
      isNotFound: {
        type: Boolean, default: false
      },

      /**
       * Интервал повтора событий visible (мс)
       */
      interval: {
        type: Number, default: 500
      },

      /**
       * Имя события - лоадер виден (по умолчанию visible)
       */
      visibleEventName: {
        type: String, default: 'visible'
      },

      /**
       * ID скролл элемента (по умолчанию window)
       */
      scrollElementId: {
        type: String, default: ''
      },

      /**
       * Мини-версия?
       */
      mini: {
        type: Boolean, default: false
      },

      /**
       * Выравнивание по ширине
       */
      horizontalAlign: {
        type: String, default: 'left',
        validator: function (value) {
          return ['left', 'right', 'center'].indexOf(value) !== -1;
        }
      }
    },

    data() {
      return {
        inViewport: false,
        intervalId: null,
        scrollElement: window
      };
    },

    mounted() {
      if (this.scrollElementId.length) {
        this.scrollElement = document.getElementById(this.scrollElementId);
      }
      window.addEventListener('resize', this.handleUserActivity);
      this.scrollElement.addEventListener('scroll', this.handleUserActivity);
      this.inViewport = this.checkIfElementInViewport();
      this.setInterval();
    },

    destroyed() {
      this.clearEventListeners();
      this.clearInterval();
    },

    methods: {
      /**
       * Обрабатывает прокрутку и ресайз окна
       * Если нужен тип события, можно принимать параметр event
       */
      handleUserActivity() {
        let inViewportNow = this.checkIfElementInViewport();
        // Если состояние изменилось, создаем событие
        if (inViewportNow !== this.inViewport) {
          this.$emit(inViewportNow ? this.visibleEventName : 'hide');
        }

        if (inViewportNow === true) {
          this.setInterval();
        }
        this.inViewport = inViewportNow;
      },

      clearInterval() {
        if (this.intervalId) {
          clearInterval(this.intervalId);
        }
      },

      clearEventListeners() {
        window.removeEventListener('resize', this.handleUserActivity);
        this.scrollElement.removeEventListener('scroll', this.handleUserActivity);
      },

      setInterval() {
        this.clearInterval();
        this.intervalId = setInterval(() => {
          if (this.isLoadingDone) {
            this.clearInterval();
          }
          if (this.checkIfElementInViewport()) {
            this.$emit(this.visibleEventName);
          } else {
            if (this.isLoadingDone) {
              this.$emit('hide');
              this.clearInterval();
            }
          }
        }, this.interval);
      },

      /**
       * Проверяет видимость элемента на экране
       * @returns {boolean}
       */
      checkIfElementInViewport() {
        let rect = this.$el.getBoundingClientRect();
        return (
          rect.top >= 0 &&
          rect.left >= 0 &&
          (rect.bottom - 16) <= (this.scrollElement.clientHeight ? this.scrollElement.getBoundingClientRect().bottom : (window.innerHeight || document.documentElement.clientHeight))
        );
      }
    },

    watch: {
      /**
       * Следит за параметром isLoadingDone, устанавливает интервал
       * Если всё загружено и лоадер не нужен, то удаляет обработчики window.resize и window.scroll
       */
      isLoadingDone(){
        this.setInterval();
        if (this.isLoadingDone) {
          this.clearEventListeners();
        }
      }
    }
  };
</script>

<style lang="css">
  .vue-infinite-spinner {
    display: flex;
    flex-direction: row;
    align-items: center;
  }

  .vue-infinite-spinner_left {
    margin-right: auto;
  }

  .vue-infinite-spinner_right {
    margin-left: auto;
  }

  .vue-infinite-spinner_center {
    margin-right: auto;
    margin-left: auto;
  }

  .vue-infinite-spinner_mini {
    margin: 0;
  }

  .vue-infinite-spinner__spinner {
    width: 16px;
    margin-right: 8px;
    animation-name: spin;
    animation-duration: 1000ms;
    animation-iteration-count: infinite;
    animation-timing-function: linear;
  }

  .vue-infinite-spinner__message {
    height: 13px;
    line-height: 16px;
  }

  .vue-infinite-spinner__message span {
    margin-left: 1px;
    animation-name: dots;
    animation-duration: 750ms;
    animation-iteration-count: infinite;
    animation-timing-function: linear;
  }

  .vue-infinite-spinner__message span:nth-child(1) {
    animation-delay: 250ms;
  }

  .vue-infinite-spinner__message span:nth-child(2) {
    animation-delay: 400ms;
  }

  .vue-infinite-spinner__message span:nth-child(3) {
    animation-delay: 550ms;
  }

  .vue-infinite-spinner__message_mini {
    font-style: italic;
    font-size: 11px;
    color: gray;
  }

  @keyframes spin {
    from {
      transform: rotate(0deg);
    }

    to {
      transform: rotate(360deg);
    }
  }

  @keyframes dots {
    from {
      opacity: 1;
    }

    to {
      opacity: 0;
    }
  }
</style>
