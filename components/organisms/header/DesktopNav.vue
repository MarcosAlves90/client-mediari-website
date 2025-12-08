<script setup lang="ts">
  import { ref, computed, onMounted, onUnmounted } from 'vue'
  import { useI18n } from 'vue-i18n'
  import { useContacts } from '@/utils/useContacts'
  import { useScreenWidth } from '@/utils/useScreenWidth'
  import DropdownMenu from '@/components/molecules/DropdownMenu.vue'
  import { CONTACT_INFO } from '~/constants'

  const { t } = useI18n()
  import { useLocalePathSafe } from '~/composables/useLocalePathSafe'
  const localePath = useLocalePathSafe()
  const { openPhoneDialer } = useContacts()
  const screenWidth = useScreenWidth()

  interface Props {
    isNavbarSmall: boolean
    onNavClick: (link: { section?: string; href?: string }) => void
  }

  const { onNavClick } = defineProps<Props>()

  const sectionsDropdownOpen = ref(false)

  const sectionLinks = computed(() => [
    { label: t('navbar.services'), section: 'services-section' },
    { label: t('navbar.enterprise'), section: 'enterprise-section' },
    { label: t('navbar.founder'), section: 'seo-section' },
    { label: t('navbar.team'), section: 'team-section' },
    { label: t('navbar.socialAction'), section: 'social-action-section' },
    { label: t('navbar.contact'), section: 'contact-section' },
  ])

  const handleNavClick = (link: { section?: string; href?: string }) => {
    onNavClick(link)
    sectionsDropdownOpen.value = false
  }

  const toggleSectionsDropdown = () => {
    sectionsDropdownOpen.value = !sectionsDropdownOpen.value
  }

  const closeSectionsDropdown = () => {
    sectionsDropdownOpen.value = false
  }

  const handleKeydown = (event: KeyboardEvent) => {
    if (event.key === 'Escape' || event.key === 'Esc') {
      sectionsDropdownOpen.value = false
    }
  }

  onMounted(() => {
    document.addEventListener('keydown', handleKeydown)
  })

  onUnmounted(() => {
    document.removeEventListener('keydown', handleKeydown)
  })
</script>

<template>
  <nav
    class="app-header__nav app-header__nav--desktop flex items-center justify-center gap-[2.8rem]"
    v-if="screenWidth >= 1024"
    aria-label="Main navigation"
  >
    <a
      :href="localePath('/')"
      @click.prevent="handleNavClick({ href: '/' })"
      class="after:absolute after:bottom-[-3px] after:left-1/2 after:-translate-x-1/2 after:w-0 after:bg-accent-color after:content-[''] after:h-[2px] hover:after:w-full no-underline"
      key="desktop-nav-home"
    >
      {{ t('navbar.home') }}
    </a>

    <!-- Dropdown de Seções -->
    <div class="relative" ref="dropdownRef">
      <button
        @click.stop="toggleSectionsDropdown"
        :aria-expanded="sectionsDropdownOpen"
        aria-haspopup="menu"
        :aria-controls="'sections-menu'"
        class="flex items-center gap-1 bg-transparent border-none cursor-pointer text-primary-text-color"
      >
        {{ t('navbar.sections') }}
        <Icon
          name="mdi:chevron-down"
          class="text-[1.2rem] transition-transform duration-200"
          :class="{ 'rotate-180': sectionsDropdownOpen }"
        />
      </button>

      <DropdownMenu
        :isOpen="sectionsDropdownOpen"
        @close="closeSectionsDropdown"
      >
        <div id="sections-menu" role="menu" class="flex flex-col">
          <a
            v-for="(link, idx) in sectionLinks"
            :key="`sections-${idx}`"
            :href="'#' + link.section"
            role="menuitem"
            @click.prevent="handleNavClick(link)"
            class="block px-4 py-1 text-sm text-gray-700 hover:bg-accent-color/20 hover:text-accent-color transition-colors duration-200 no-underline"
          >
            {{ link.label }}
          </a>
        </div>
      </DropdownMenu>
    </div>

    <a
      :href="localePath('/trabalhe-conosco')"
      @click.prevent="handleNavClick({ href: '/trabalhe-conosco' })"
      class="after:absolute after:bottom-[-3px] after:left-1/2 after:-translate-x-1/2 after:w-0 after:bg-accent-color after:content-[''] after:h-[2px] hover:after:w-full no-underline"
      key="desktop-nav-careers"
    >
      {{ t('navbar.careers') }}
    </a>

    <button
      v-if="screenWidth >= 1280"
      class="app-header__nav-button common-button"
      @click.prevent="openPhoneDialer"
      :aria-label="t('navbar.callPhone', { phone: CONTACT_INFO.phone.display })"
    >
      <Icon
        class="app-header__nav-button-icon text-[1.3rem]"
        name="mdi:phone-outline"
      />
      {{ CONTACT_INFO.phone.display }}
    </button>
  </nav>
</template>

<style scoped>
  .app-header__nav {
    transition: font-size 0.2s ease-in-out;
  }

  .app-header__nav a {
    position: relative;
    color: var(--color-primary-text);
  }

  .app-header__nav--desktop a::after {
    transition: width 0.2s ease-in-out;
  }
</style>
