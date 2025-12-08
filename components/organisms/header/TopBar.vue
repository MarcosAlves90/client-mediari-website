<script setup lang="ts">
  import { useScreenWidth } from '@/utils/useScreenWidth'
  import { ref } from 'vue'
  import DropdownMenu from '@/components/molecules/DropdownMenu.vue'
  import { CONTACT_INFO } from '~/constants'

  const screenWidth = useScreenWidth()

  interface Props {
    isNavbarSmall: boolean
  }

  defineProps<Props>()

  interface ContactLink {
    href: string
    icon: string
    text: string
    target: string
    rel: string
    show: () => boolean
    isDropdown?: boolean
  }

  const showWhatsappMenu = ref(false)

  const contactLinks: ContactLink[] = [
    {
      href: CONTACT_INFO.email.link,
      icon: 'mdi:email-outline',
      text: CONTACT_INFO.email.display,
      target: '',
      rel: '',
      show: () => true,
    },
    {
      href: CONTACT_INFO.socialMedia.instagram.link,
      icon: 'mdi:instagram',
      text: CONTACT_INFO.socialMedia.instagram.display,
      target: '_blank',
      rel: 'noopener noreferrer',
      show: () => true,
    },
    {
      href: CONTACT_INFO.socialMedia.linkedin.link,
      icon: 'mdi:linkedin',
      text: CONTACT_INFO.socialMedia.linkedin.display,
      target: '_blank',
      rel: 'noopener noreferrer',
      show: () => true,
    },
    {
      href: '#',
      icon: 'mdi:whatsapp',
      text: 'WhatsApp',
      target: '',
      rel: '',
      show: () => true,
      isDropdown: true,
    },
    {
      href: CONTACT_INFO.phone.link,
      icon: 'mdi:phone-outline',
      text: CONTACT_INFO.phone.display,
      target: '',
      rel: '',
      show: () => screenWidth.value < 1280,
    },
  ]
</script>

<template>
  <div
    class="app-header__top flex items-center justify-center gap-1 bg-accent-color py-0.5 text-sm text-accent-text-color transition-all duration-200 ease-in-out max-md:justify-evenly"
    :class="{ 'app-header--small': isNavbarSmall }"
  >
    <template v-for="(link, idx) in contactLinks" :key="idx">
      <div v-if="link.isDropdown && link.show()" class="relative">
        <button
          class="app-header__top-link flex cursor-pointer items-center gap-0.5 rounded-sm no-underline transition-colors duration-200 ease-in-out hover:bg-body-bg-2 bg-transparent border-none text-inherit p-0"
          @click.prevent.stop="showWhatsappMenu = !showWhatsappMenu"
          :aria-label="link.text"
        >
          <Icon
            class="app-header__top-link-icon text-[1.8rem] transition-[font-size] duration-200 ease-in-out max-lg:text-2xl"
            :name="link.icon"
          />
          <p
            class="m-0 max-lg:text-xs transition-[font-size] duration-200 ease-in-out"
            v-if="!(screenWidth < 768)"
          >
            {{ link.text }}
          </p>
          <Icon
            name="mdi:chevron-down"
            class="transition-all duration-200 text-lg app-header__chevron"
            :class="showWhatsappMenu ? 'rotate-180' : ''"
          />
        </button>

        <DropdownMenu
          :isOpen="showWhatsappMenu"
          @close="showWhatsappMenu = false"
        >
          <a
            v-for="wa in CONTACT_INFO.whatsapp"
            :key="wa.display"
            :href="wa.link"
            target="_blank"
            rel="noopener noreferrer"
            class="px-1 py-1 text-sm text-gray-700 hover:bg-accent-color/20 hover:text-accent-color transition-colors duration-200 no-underline flex items-center gap-0.5"
            @click="showWhatsappMenu = false"
          >
            <Icon name="mdi:whatsapp" class="text-green-600 text-lg" />
            {{ wa.display }}
          </a>
        </DropdownMenu>
      </div>

      <a
        v-else-if="link.show()"
        class="app-header__top-link flex cursor-pointer items-center gap-0.5 rounded-sm no-underline transition-colors duration-200 ease-in-out hover:bg-body-bg-2"
        :href="link.href"
        :target="link.target"
        :rel="link.rel"
        :aria-label="link.text"
      >
        <Icon
          class="app-header__top-link-icon text-[1.8rem] transition-[font-size] duration-200 ease-in-out max-lg:text-2xl"
          :name="link.icon"
        />
        <p
          class="m-0 max-lg:text-xs transition-[font-size] duration-200 ease-in-out"
          v-if="!(screenWidth < 768)"
        >
          {{ link.text }}
        </p>
      </a>
    </template>
  </div>
</template>

<style scoped>
  @media (min-width: 768px) {
    .app-header--small .app-header__top {
      padding: 6px 0;
    }
    .app-header--small .app-header__top-link-icon {
      font-size: 0;
    }
    .app-header--small .app-header__top-link p {
      font-size: 0;
    }
    .app-header--small .app-header__chevron {
      font-size: 0;
    }
  }
</style>
