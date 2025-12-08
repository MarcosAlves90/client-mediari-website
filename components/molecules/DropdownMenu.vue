<script setup lang="ts">
  import { ref, nextTick, watch, onMounted, onUnmounted } from 'vue'

  const props = defineProps<{
    isOpen: boolean
    // Optional: element to calculate position against. If not provided, uses parent.
    reference?: HTMLElement | null
  }>()

  const emit = defineEmits<{
    (e: 'close'): void
  }>()

  const dropdownPosition = ref<'top' | 'bottom'>('bottom')
  const menuRef = ref<HTMLElement | null>(null)

  const calculatePosition = () => {
    const el = props.reference || menuRef.value?.parentElement
    if (el) {
      const rect = el.getBoundingClientRect()
      const spaceBelow = window.innerHeight - rect.bottom
      dropdownPosition.value = spaceBelow > 140 ? 'bottom' : 'top'
    }
  }

  const handleClickOutside = (event: MouseEvent) => {
    if (!props.isOpen) return
    const target = event.target as Node
    if (menuRef.value && !menuRef.value.contains(target)) {
      emit('close')
    }
  }

  onMounted(() => {
    document.addEventListener('click', handleClickOutside)
  })

  onUnmounted(() => {
    document.removeEventListener('click', handleClickOutside)
  })

  watch(
    () => props.isOpen,
    async (newValue) => {
      if (newValue) {
        await nextTick()
        calculatePosition()
      }
    }
  )
</script>

<template>
  <Transition name="dropdown-fade">
    <div
      v-show="isOpen"
      ref="menuRef"
      :class="[
        'absolute left-1/2 transform -translate-x-1/2 w-max rounded bg-body-bg shadow-lg border-2 border-black z-50 transition duration-200 ease-in-out',
        dropdownPosition === 'bottom' ? 'top-full mt-1' : 'bottom-full mb-1',
      ]"
    >
      <div class="h-0.5 bg-accent-color" />
      <slot></slot>
      <div class="h-0.5 bg-accent-color" />
    </div>
  </Transition>
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
</style>
