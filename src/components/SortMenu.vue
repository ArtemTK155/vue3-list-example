<template>
  <div class="relative w-full items-center max-w-60">
    <div class="relative group/bouton w-full">
      <button class="bg-white text-left py-3 pl-4 min-w-44 relative w-full">
        Sort by:
        <span class="font-bold pl-2">
          {{ displayText }}
        </span>
        <span
          class="absolute flex items-center justify-center bg-stone-200 w-12 top-0 h-full right-0"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6 group-hover/bouton:rotate-90"
          >
            <path stroke-linecap="round" stroke-linejoin="round" d="m8.25 4.5 7.5 7.5-7.5 7.5" />
          </svg>
        </span>
      </button>
      <div
        class="absolute w-full bg-white top-ful origine-top opacity-0 hidden flex-col group-hover/bouton:flex group-hover/bouton:opacity-100 transition-all"
      >
        <label
          class="flex radio p-2 cursor-pointer px-4 border-b border-stone-200"
          @click="onOptionClick(SortingOptions.Amount)"
        >
          <input class="my-auto transform scale-125" checked type="radio" name="sfg" />
          <div class="title px-2">Amount</div>
        </label>

        <label
          class="flex radio p-2 cursor-pointer px-4 border-b border-stone-200"
          @click="onOptionClick(SortingOptions.State)"
        >
          <input class="my-auto transform scale-125" type="radio" name="sfg" />
          <div class="title px-2">State</div>
        </label>

        <label
          class="flex radio p-2 cursor-pointer px-4 border-b border-stone-200"
          @click="onOptionClick(SortingOptions.Date)"
        >
          <input class="my-auto transform scale-125" type="radio" name="sfg" />
          <div class="title px-2">Date</div>
        </label>

        <label
          class="flex radio p-2 cursor-pointer px-4 border-b border-stone-200"
          @click="onOptionClick(SortingOptions.ProjectId)"
        >
          <input class="my-auto transform scale-125" type="radio" name="sfg" />
          <div class="title px-2">Project</div>
        </label>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { SortingOptions } from '@/enums'

const displayText = ref<string>('')

const emit = defineEmits(['selectOption'])

const onOptionClick = (op: SortingOptions) => {
  emit('selectOption', op)
  switch (op) {
    case SortingOptions.Amount:
      displayText.value = 'Amount'
      break

    case SortingOptions.State:
      displayText.value = 'State'
      break

    case SortingOptions.Date:
      displayText.value = 'Date'
      break

    case SortingOptions.ProjectId:
      displayText.value = 'Project'
      break
    default:
      break
  }
}

onMounted(() => {
  //on mount filter by amount
  emit('selectOption', SortingOptions.Amount)
  displayText.value = 'Amount'
})
</script>
