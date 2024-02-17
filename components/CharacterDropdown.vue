
import type { mergeProps } from 'vue';
<template>
  <Menu as="div" class="relative inline-block text-left">
    <div>
      <MenuButton
        class="inline-flex w-full justify-center gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50">
        {{ character }}
      </MenuButton>
    </div>

    <transition enter-active-class="transition ease-out duration-100" enter-from-class="transform opacity-0 scale-95"
      enter-to-class="transform opacity-100 scale-100" leave-active-class="transition ease-in duration-75"
      leave-from-class="transform opacity-100 scale-100" leave-to-class="transform opacity-0 scale-95">
      <MenuItems v-if="symbolArray.length"
        class="absolute left-0 z-10 mt-2 w-56 origin-top-left rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none">
        <div v-for="alias in symbolArray" :key="alias" class="py-1">
          <MenuItem v-if="alias" v-slot="{ active }">
            <a href="#" :class="[active ? 'bg-gray-100 text-gray-900' : 'text-gray-700', 'block px-4 py-2 text-sm']"
              @click="onClickAlias(alias)">
              {{ alias }}
            </a>
          </MenuItem>
        </div>
      </MenuItems>
    </transition>
  </Menu>
</template>

<script setup lang="ts">
import { Menu, MenuButton, MenuItem, MenuItems } from '@headlessui/vue'

const emits = defineEmits(['update:character'])

const props = defineProps({
  character: {
    type: String,
    required: true,
  },
  position: {
    type: Number,
    required: true,
  }
})

const { getSymbolArray } = useSymbol()

const symbolArray = computed(() => {
  return getSymbolArray(props.character)
})

const onClickAlias = (alias: string) => {
  emits('update:character', {
    index: props.position,
    character: alias
  })
}
</script>
