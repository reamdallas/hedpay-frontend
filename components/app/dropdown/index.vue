<template>
  <div class="dropdown">
    <button
      class="dropdown-button"
      @click="toggleMenu"
    >
      <svg viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path
            d="M17.14 10.9361C17.176 10.6361 17.2 10.3241 17.2 10.0001C17.2 9.67605 17.176 9.36406 17.128 9.06406L19.156 7.48005C19.243 7.40717 19.3024 7.30665 19.3242 7.19527C19.3461 7.0839 19.329 6.9684 19.276 6.86805L17.356 3.54406C17.236 3.32806 16.984 3.25606 16.768 3.32806L14.38 4.28806C13.8841 3.90527 13.3393 3.59048 12.76 3.35206L12.4 0.808055C12.3826 0.693598 12.3245 0.589269 12.2363 0.514293C12.1481 0.439318 12.0358 0.398746 11.92 0.400055H8.07999C7.96554 0.398894 7.85465 0.439805 7.76837 0.515019C7.6821 0.590234 7.62645 0.694513 7.61199 0.808055L7.25199 3.35206C6.67368 3.59246 6.1291 3.9071 5.63199 4.28806L3.24399 3.32806C3.13646 3.2866 3.01752 3.28565 2.90934 3.32539C2.80116 3.36513 2.71112 3.44285 2.65599 3.54406L0.735992 6.86805C0.676613 6.9668 0.656012 7.08408 0.678183 7.19715C0.700355 7.31023 0.763719 7.41104 0.855992 7.48005L2.88399 9.06406C2.83599 9.36406 2.79999 9.68806 2.79999 10.0001C2.79999 10.3121 2.82399 10.6361 2.87199 10.9361L0.843992 12.5201C0.756988 12.5929 0.697612 12.6935 0.675773 12.8048C0.653934 12.9162 0.670954 13.0317 0.723992 13.1321L2.64399 16.4561C2.76399 16.6721 3.01599 16.7441 3.23199 16.6721L5.61999 15.7121C6.11588 16.0948 6.6607 16.4096 7.23999 16.6481L7.59999 19.1921C7.64799 19.4321 7.83999 19.6001 8.07999 19.6001H11.92C12.16 19.6001 12.364 19.4321 12.388 19.1921L12.748 16.6481C13.3263 16.4076 13.8709 16.093 14.368 15.7121L16.756 16.6721C16.972 16.7561 17.224 16.6721 17.344 16.4561L19.264 13.1321C19.3234 13.0333 19.344 12.916 19.3218 12.803C19.2996 12.6899 19.2363 12.5891 19.144 12.5201L17.14 10.9361ZM9.99999 13.6001C8.01999 13.6001 6.39999 11.9801 6.39999 10.0001C6.39999 8.02006 8.01999 6.40005 9.99999 6.40005C11.98 6.40005 13.6 8.02006 13.6 10.0001C13.6 11.9801 11.98 13.6001 9.99999 13.6001Z"
            fill="#2DCCD3"
          />
      </svg>
    </button>
    <ul
      v-show="isOpened"
      class="dropdown-menu"
      v-click-outside="hideMenu">
      <li
        v-for="(item, index) in items"
        :key="index"
        :class="{ 'dropdown-menu__item__active': item.name===value.name }"
        class="dropdown-menu__item"
        @click="selectOption(item)"
      >{{item.name}}</li>
    </ul>
  </div>
</template>

<script>
import ClickOutside from 'vue-click-outside';

export default {
  name: 'dropdown',
  props: {
    items: {
      type: Array,
      required: true,
    },
    value: {
      required: true,
    },
  },
  data() {
    return {
      isOpened: false,
    };
  },
  methods: {
    toggleMenu() {
      this.isOpened = !this.isOpened;
    },
    hideMenu() {
      this.isOpened = false;
    },
    selectOption(option) {
      this.$emit('input', option);
      this.hideMenu();
    },
  },
  mounted() {
    this.popupItem = this.$el;
  },
  directives: {
    ClickOutside,
  },
};
</script>

<style lang="scss">
  .dropdown {
    position: relative;
  }
  button.dropdown-button {
    width: 19px;
    height: 19px;
    padding: 0;
    background-color: transparent;
    svg {
      width: 100%;
      height: 100%;
    }
  }
  .dropdown-menu {
    position: absolute;
    top: 32px;
    right: 0px;
    width: 190px;
    background-color: $white;
    opacity: 1;
    box-shadow: 0px 15px 40px rgba(0, 0, 0, 0.15);
    border-radius: 10px;
    overflow: hidden;
    &__item {
      padding: 12px 20px;
      cursor: pointer;
      &:hover {
        background-color: $stroke;
      }
      &__active {
        background-color: $stroke2;
      }
    }
  }
</style>
