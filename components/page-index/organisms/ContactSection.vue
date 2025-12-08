<script setup lang="ts">
  import { useI18n } from 'vue-i18n'
  import { ref } from 'vue'
  import ContactCard from '~/components/page-index/molecules/ContactCard.vue'
  import { useContacts } from '~/utils/useContacts'
  import { useHomepageStyles } from '~/composables/page-index/useHomepageStyles'
  import DropdownMenu from '@/components/molecules/DropdownMenu.vue'
  import { CONTACT_INFO } from '~/constants'

  const { t } = useI18n()
  const { openPhoneDialer, openMailTo, openLinkInBrowser } = useContacts()
  const {
    homepage__container,
    homepage__section_title,
    homepage__section_subtitle,
    homepage__section_description,
  } = useHomepageStyles()

  const showWhatsappMenu = ref(false)
</script>

<template>
  <section
    id="contact-section"
    class="homepage__contact homepage__contact--gray-bg w-full flex justify-center items-center bg-body-bg-dark"
    role="region"
    aria-labelledby="contact-heading"
    aria-describedby="contact-description"
  >
    <div
      :class="[
        homepage__container,
        'homepage__contact-content max-w-85 w-full',
      ]"
    >
      <p
        id="contact-heading"
        class="homepage__section-subtitle"
        :class="homepage__section_subtitle"
      >
        {{ t('contact.section_subtitle') }}
      </p>
      <h2 class="homepage__section-title" :class="homepage__section_title">
        {{ t('contact.section_title') }}
      </h2>
      <p
        id="contact-description"
        class="homepage__section_description"
        :class="homepage__section_description"
      >
        {{ t('contact.description') }}
      </p>
      <div
        class="homepage__contact-list mt-1 flex items-center justify-between gap-0.5 max-md:grid max-md:grid-cols-2"
        role="list"
        aria-label="Opções de contato"
      >
        <ContactCard
          backgroundImage="instagram_g4nm4d_vkd5ws.webp"
          iconImage="instagram"
          :buttonText="t('contact.instagram')"
          shortButtonText="Instagram"
          :buttonAction="
            () => openLinkInBrowser(CONTACT_INFO.socialMedia.instagram.link)
          "
          role="listitem"
          aria-label="Contato pelo Instagram"
        />

        <ContactCard
          backgroundImage="telefone_bluhgk_p8rms9.webp"
          iconImage="telefone"
          :buttonText="t('contact.phone')"
          shortButtonText="Telefone"
          :buttonAction="openPhoneDialer"
          role="listitem"
          aria-label="Contato por telefone"
        />

        <div class="relative w-full" ref="whatsappDropdownContainer">
          <ContactCard
            backgroundImage="whatsapp-print_dinoqb.jpg"
            :imgModifiers="{ c: 'crop', h: 350, w: 309, g: 'north' }"
            iconImage="whatsapp"
            buttonText="WhatsApp"
            shortButtonText="WhatsApp"
            :buttonAction="
              (e: Event) => {
                e.stopPropagation()
                showWhatsappMenu = !showWhatsappMenu
              }
            "
            :dropdownState="showWhatsappMenu ? 1 : 0"
            role="listitem"
            aria-label="Contato por WhatsApp"
          >
            <template #dropdown>
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
            </template>
          </ContactCard>
        </div>

        <ContactCard
          backgroundImage="gmail_itiiyf_qn4gk0.webp"
          iconImage="gmail"
          :buttonText="t('contact.email')"
          shortButtonText="E-mail"
          :buttonAction="openMailTo"
          role="listitem"
          aria-label="Contato por e-mail"
        />

        <ContactCard
          backgroundImage="linkedin_jbmidd_faecil.webp"
          iconImage="linkedin"
          :buttonText="t('contact.linkedin')"
          shortButtonText="LinkedIn"
          :buttonAction="
            () => openLinkInBrowser(CONTACT_INFO.socialMedia.linkedin.link)
          "
          role="listitem"
          aria-label="Contato pelo LinkedIn"
          class="max-md:col-span-2 linkedin-card"
        />
      </div>
    </div>
  </section>
</template>

<style scoped>
  .dropdown-fade-enter-active,
  .dropdown-fade-leave-active {
    transition:
      opacity 0.2s ease-in-out,
      transform 0.2s ease-in-out;
  }

  .dropdown-fade-enter-from,
  .dropdown-fade-leave-to {
    opacity: 0;
    transform: translateY(8px) scale(0.95);
  }

  .dropdown-fade-enter-to,
  .dropdown-fade-leave-from {
    opacity: 1;
    transform: translateY(0) scale(1);
  }

  @media (max-width: 767px) {
    :deep(.linkedin-card .contact-card__square) {
      aspect-ratio: 2/1;
      height: auto !important;
    }
  }
</style>
