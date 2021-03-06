<template>
  <footer
    :class="{
      'bg-primary-darkest text-primary-lightest border-primary-darker':
        background === 'dark',
      'bg-primary-lighter border-primary-med': background === 'light'
    }"
    class="border-t"
    data-sw-path="footer"
  >
    <!-- Main footer -->
    <div class="container py-16 text-center lg:flex lg:flex-row lg:text-left">
      <!-- Store info -->
      <div v-if="footer.showContactInfo || footer.showSocial" class="lg:w-1/4 lg:pr-6">
        <div v-if="footer.showContactInfo" class="mb-5">
          <p v-if="footer.contactInfoHeading" class="text-sm mb-4">
            {{ footer.contactInfoHeading }}
          </p>
          <p>
            <a class="block" :href="'tel:' + store.supportPhone">
              {{ store.supportPhone }}
            </a>
            <a class="block" :href="'mailto:' + store.supportEmail">
              {{ store.supportEmail }}
            </a>
          </p>
          <div class="mt-2" v-html="footer.contactInfoText"></div>
        </div>

        <!-- Social links-->
        <ul v-if="footer.showSocial" class="mx-auto lg:-ml-2" data-sw-path="social">
          <li v-for="link of orderedSocialLinks" :key="link.id" class="inline-block mb-0">
            <a :href="link.url" target="_blank" :title="`Join us on ${link.id}`" class="block mx-2">
              <BaseIcon :icon="`mdi:${link.id}`" />
            </a>
          </li>
        </ul>
      </div>

      <!-- Nav menu -->
      <template v-if="menu">
        <div
          v-for="(column, index) in menu.items"
          :key="'footerMenuCol' + index"
          class="my-10 lg:w-1/4 lg:my-0"
        >
          <ul v-if="column.items">
            <li v-if="showMenuLabel" class="text-sm mb-3">Explore</li>
            <li v-for="(item, i) in column.items" :key="item.name" class="mb-0">
              <!-- Heading -->
              <p
                v-if="item.type === 'heading'"
                :class="{ 'mt-6': i > 0 }"
                class="text-sm text-primary-med mb-3"
              >
                {{ item.name }}
              </p>
              <!-- Standard link -->
              <NuxtLink
                v-else
                :to="resolveUrl(item)"
                class="inline-block py-1 leading-tight text-xl"
                >{{ item.name }}</NuxtLink
              >
            </li>
          </ul>
        </div>
      </template>

      <!-- Email signup -->
      <template v-if="footer.showEmailSignup">
        <div class="max-w-96 mx-auto lg:w-1/4 lg:flex-shrink-0">
          <!-- Heading + text -->
          <p v-if="footer.emailSignupHeading" class="text-sm mb-5">
            {{ footer.emailSignupHeading }}
          </p>
          <div v-if="footer.emailSignupText" v-html="footer.emailSignupText"></div>
          <EmailSignupForm :theme="background === 'dark' ? 'dark' : 'light'" class="mt-5" />
        </div>
      </template>
    </div>
    <!-- END Main footer -->

    <!-- Secondary footer -->
    <div
      :class="{
        'border-primary-darker': background === 'dark',
        'border-primary-med': background === 'light'
      }"
      class="py-4 border-t border-primary-darker text-sm text-center"
    >
      <div class="container lg:flex lg:flex-no-wrap lg:justify-between">
        <!-- Copyright & Settings -->
        <p class="my-3 text-sm">
          <span>©{{ currentYear }} {{ store.name }}.</span> Powered by
          <a
            id="powered-by-swell"
            href="https://www.swell.is/"
            title="Headless ecommerce platform for brands"
            class="text-current"
            >Swell</a
          >
        </p>

        <!-- Secondary nav menu -->
        <ul v-if="footer.showSecondaryMenu && secondaryMenu" class="my-3">
          <li v-for="item in secondaryMenu.items" :key="item.name" class="inline-block mx-3 mb-0">
            <NuxtLink :to="resolveUrl(item)">{{ item.name }}</NuxtLink>
          </li>
        </ul>

        <!-- Payment methods TODO -->
        <ul
          v-if="footer.showPaymentMethods && paymentMethods"
          class="flex flex-wrap justify-center"
        >
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
        </ul>
      </div>
    </div>
  </footer>
</template>

<script>
// Helpers
import get from 'lodash/get'

export default {
  name: 'TheFooter',

  fetch() {
    const { $swell } = this

    // Get menu settings
    const menuId = $swell.settings.get('footer.menu', 'footer')
    const secondaryMenuId = $swell.settings.get('footer.secondaryMenu', 'footer-secondary')

    // Set menus
    this.menu = $swell.settings.menus(menuId)
    this.secondaryMenu = $swell.settings.menus(secondaryMenuId)

    // Set component data
    this.store = $swell.settings.get('store', {})
    this.footer = $swell.settings.get('footer', {})
    this.socialLinks = $swell.settings.get('socialLinks', {})
    this.background = $swell.settings.get('footer.background', 'dark')
  },

  data() {
    return {
      footer: {},
      store: {},
      socialLinks: {},
      menu: null,
      secondaryMenu: null,
      paymentMethods: null,
      currentYear: new Date().getFullYear(),
      background: 'dark'
    }
  },

  computed: {
    orderedSocialLinks() {
      return ['twitter', 'facebook', 'pinterest', 'instagram', 'youtube', 'vimeo']
        .map(id => ({ id, ...get(this, `socialLinks.${id}`, {}) })) // Get the network's link value from settings
        .filter(link => link.show && link.url) // Only include if it's switched on and  has a url
    },
    showMenuLabel() {
      const firstItem = get(this, 'menu.items.0.items.0', {})
      return firstItem.type !== 'heading'
    }
  }
}
</script>
