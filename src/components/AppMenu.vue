<template>
  <div class="q-menu-wrapper">
    <div :class="{ open: isMenuOpen, 'menu-icon': true }" @click="toggleMenu">
      <div class="bar1"></div>
      <div class="bar2"></div>
      <div class="bar3"></div>
    </div>
    <transition name="fade">
      <div v-if="isMenuOpen" class="menu-overlay">
        <menu>
          <router-link to="/" @click.stop.native="closeMenu">BACK TO THE MAIN PAGE</router-link>
          <router-link
            v-if="currentLevelID > 0"
            :to="continueLink"
            @click.stop.native="handleContinueClick"
            >CONTINUE</router-link
          >
          <router-link
            v-for="item in menuItems"
            :key="item.name"
            :to="item.url"
            @click.stop.native="closeMenu"
            >{{ item.name }}</router-link
          >
          <a href="https://medium.com/quantum-photons" target="_blank">BLOG</a>
        </menu>
      </div>
    </transition>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator'
import { State, namespace } from 'vuex-class'

const user = namespace('userModule')

@Component
export default class AppMenu extends Vue {
  @State('currentLevelID') currentLevelID!: number
  @user.Getter('isLoggedIn') moduleGetterIsLoggedIn!: boolean

  isMenuOpen = false

  created(): void {
    window.addEventListener('keyup', this.handleEscPress)
  }

  closeMenu(): void {
    this.isMenuOpen = false
  }

  handleContinueClick(e: MouseEvent): void {
    // if we are in the encyclopedia, the continue
    // should take us to the previous played level
    // otherwuise in case we are playing:
    if (this.$route.name === 'level') {
      e.preventDefault()
      this.closeMenu()
    }
  }

  toggleMenu(): void {
    this.isMenuOpen = !this.isMenuOpen
  }

  handleEscPress(e: { which: number }): void {
    if (e.which === 27) {
      this.toggleMenu()
    }
  }

  beforeDestroy(): void {
    window.removeEventListener('keyup', this.handleEscPress)
  }

  get continueLink(): string {
    return `/level/${this.currentLevelID}`
  }

  /**
   * Created to iterate over in template;
   * the second element depends on whether
   * the user is logged in.
   * @remarks showing link to login page
   * for users logged in seemed silly.
   */
  get menuItems(): { name: string; url: string }[] {
    return [
      {
        name: 'LEVELS',
        url: '/levels',
      },
      this.moduleGetterIsLoggedIn
        ? { name: 'MY ACCOUNT', url: '/myaccount' }
        : { name: 'LOGIN', url: '/login' },
      {
        name: 'SANDBOX',
        url: '/sandbox',
      },
      {
        name: 'ENCYCLOPEDIA',
        url: '/info',
      },
      {
        name: 'OPTIONS',
        url: '/options',
      },
    ]
  }
}
</script>

<style scoped lang="scss">
.q-menu-wrapper {
  min-height: 42px;
  @media screen and (max-width: 1000px) {
    width: 6vw;
    min-height: 0px;
  }
}
.menu-icon {
  display: block;
  position: absolute;
  top: 20px;
  left: 20px;
  cursor: pointer;
  z-index: 4;
  @media screen and (max-width: 1000px) {
    position: static;
    top: 2vw;
    left: 5vw;
  }
  &.open {
    position: fixed;
  }
  .bar1,
  .bar2,
  .bar3 {
    width: 35px;
    height: 3px;
    background-color: rgb(255, 255, 255);
    margin: 8px 0;
    transition: 0.4s;
    @media screen and (max-width: 1000px) {
      width: 6vw;
      height: 0.5vw;
      margin: 1vw 0;
    }
  }
  /* Rotate first bar */
  &.open .bar1 {
    transform: rotate(-45deg) translate(-7px, 3px);
    @media screen and (max-width: 1000px) {
      transform: rotate(-45deg) translate(-1.2vw, 1.2vw);
    }
  }
  /* Fade out the second bar */
  &.open .bar2 {
    opacity: 0;
  }
  /* Rotate last bar */
  &.open .bar3 {
    transform: rotate(45deg) translate(-11px, -11px);
    @media screen and (max-width: 1000px) {
      transform: rotate(45deg) translate(-1vw, -1vw);
    }
  }
}
.menu-overlay {
  z-index: 3;
  position: fixed;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  background-color: rgba(33, 2, 53, 0.9);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  cursor: unset;
  & menu {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    height: 50%;
    margin: 0;
    font-size: 2rem;
    padding-inline-start: 0;
    a {
      color: white;
      text-decoration: none;
      text-align: center;
      &:hover {
        color: white;
        font-weight: bold;
        // text-shadow: 1px 1px 2px white, -1px -1px 2px white;
      }
    }
  }
}
// Animation:
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
