<template>
  <div class="max-w-xl mx-auto pt-24">
    <label for="source-word" class="block text-sm font-medium leading-6 text-gray-50">Enter your text</label>
    <form class="flex gap-2 mt-2" @submit.prevent="word = inputValue">
      <input v-model="inputValue" type="text" name="source-word" id="source-word"
        class="block w-full rounded-md border-0 px-3 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-5" />
      <button type="button"
        class="rounded bg-indigo-600 px-2 py-1 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
        @click="word = inputValue">
        Customize
      </button>
    </form>

    <h2 class="block text-sm font-medium leading-6 text-gray-50 mt-16">Customize it (you will see the result below)</h2>
    <div v-if="word" class="flex gap-1 mt-2">

      <div v-for="(character, index) in word" :key="`character-${index}`">
        <CharacterDropdown :character="character" :key="`character-${index}`" :position="index" @update:character="onUpdateCharacter" />
      </div>
    </div>

    <h2 class="block text-sm font-medium leading-6 text-gray-50 mt-16">Copy and use it!</h2>
    <div
      class="flex flex-1 items-center justify-between truncate rounded-md border border-gray-700 bg-gray-800/50 backdrop-blur-lg mt-2">
      <div class="flex-1 truncate px-4 py-3 text-sm">
        <a href="#" class="text-2xl font-medium text-white hover:text-gray-200" @click.prevent="copy(output)">
          {{ output }}
        </a>
      </div>
      <div class="flex-shrink-0 flex items-center pr-2">
        <template v-if="isSupported">
          <a v-if="!copied" href="#" class="w-24 text-xs text-gray-300 hover:text-gray-400" @click.prevent="copy(output)">
            Click to copy
          </a>
          <span v-if="copied" class="w-24 text-xs text-gray-300">
            Copied!
          </span>
          <button type="button"
            class="inline-flex h-8 w-8 items-center justify-center rounded-full bg-transparent text-gray-300 hover:text-gray-400 focus:border-none focus:outline-none focus:ring-2 focus:ring-gray-100/20 focus:ring-offset-0"
            @click.prevent="copy(output)">
            <!-- Icon: Copy -->
            <svg v-show="!copied" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
              stroke="currentColor" class="h-5 w-5">
              <path stroke-linecap="round" stroke-linejoin="round"
                d="M9 12h3.75M9 15h3.75M9 18h3.75m3 .75H18a2.25 2.25 0 0 0 2.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 0 0-1.123-.08m-5.801 0c-.065.21-.1.433-.1.664 0 .414.336.75.75.75h4.5a.75.75 0 0 0 .75-.75 2.25 2.25 0 0 0-.1-.664m-5.8 0A2.251 2.251 0 0 1 13.5 2.25H15c1.012 0 1.867.668 2.15 1.586m-5.8 0c-.376.023-.75.05-1.124.08C9.095 4.01 8.25 4.973 8.25 6.108V8.25m0 0H4.875c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V9.375c0-.621-.504-1.125-1.125-1.125H8.25ZM6.75 12h.008v.008H6.75V12Zm0 3h.008v.008H6.75V15Zm0 3h.008v.008H6.75V18Z" />
            </svg>
            <!-- Icon: Copied -->
            <svg v-show="copied" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
              stroke="currentColor" class="h-5 w-5 text-green-300">
              <path stroke-linecap="round" stroke-linejoin="round"
                d="M11.35 3.836c-.065.21-.1.433-.1.664 0 .414.336.75.75.75h4.5a.75.75 0 0 0 .75-.75 2.25 2.25 0 0 0-.1-.664m-5.8 0A2.251 2.251 0 0 1 13.5 2.25H15c1.012 0 1.867.668 2.15 1.586m-5.8 0c-.376.023-.75.05-1.124.08C9.095 4.01 8.25 4.973 8.25 6.108V8.25m8.9-4.414c.376.023.75.05 1.124.08 1.131.094 1.976 1.057 1.976 2.192V16.5A2.25 2.25 0 0 1 18 18.75h-2.25m-7.5-10.5H4.875c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V18.75m-7.5-10.5h6.375c.621 0 1.125.504 1.125 1.125v9.375m-8.25-3 1.5 1.5 3-3.75" />
            </svg>
          </button>
        </template>
        <template v-else>
          <div class="inline-flex h-8 w-8 items-center justify-center rounded-full bg-transparent text-gray-300" disabled>
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
              stroke="currentColor" class="h-5 w-5 text-gray-400">
              <path stroke-linecap="round" stroke-linejoin="round"
                d="M18.364 18.364A9 9 0 0 0 5.636 5.636m12.728 12.728A9 9 0 0 1 5.636 5.636m12.728 12.728L5.636 5.636" />
            </svg>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const inputValue = ref<string>('test')
const word = ref<string>('test')
const output = ref<string>('test')

watch(word, (newWord) => {
  output.value = newWord
})

const onUpdateCharacter = (event: { index: number, character: string }) => {
  output.value = output.value.substring(0, event.index) + event.character + output.value.substring(event.index + 1)
}

const { text, copy, copied, isSupported } = useClipboard({ source: output })
</script>

<style>
body {
  @apply bg-gray-800;
}
</style>
