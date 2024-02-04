<template>
  <div class="bg-red-300 w-full"></div>
  <SortMenu @selectOption="handleItemSelect"></SortMenu>
  <ul class="divide-y divide-gray-100">
    <li
      v-for="item in paginatedData"
      :key="uuid"
      class="bg-white rounded-2xl w-full flex flex-col my-4"
    >
      <div class="flex bg-white space-x-4 p-4 rounded-2xl">
        <div class="h-12 w-12 flex-none rounded-full bg-gray-50 grid place-items-center">
          <div v-if="item.state !== 'paid'">
            <IconPending class="text-red-600" />
          </div>
          <div v-else>
            <IconPaid class="text-green-600" />
          </div>
        </div>
        <div class="flex-1">
          <p class="text-sm font-medium text-gray-900 truncate dark:text-white">Date</p>
          <p class="text-sm text-gray-500 truncate dark:text-gray-400"></p>
          <p v-if="item.amount" class="mt-1 text-xs leading-5 text-gray-500"></p>
          <time :datetime="item.schedule_date">{{ item.schedule_date }} </time>
        </div>
        <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
          ${{ item.amount }}
        </div>
      </div>
    </li>
  </ul>

  <div class="bg-white rounded-2xl w-full flex flex-col justify-between p-4">
    <div class="flex lg:justify-end">
      <button
        class="h-14 bg-gray-200 hover:bg-gray-300 flex items-center justify-center rounded-2xl text-13 tracking-wider font-bold uppercase cursor-pointer w-32 lg:w-32 bg-grey-6 text-grey-5"
        type="button"
        @click="goPrev()"
      >
        Back
      </button>
      <div class="flex min-w-0 gap-x-4 ml-5 mr-5">
        <div class="h-12 w-12 flex-none rounded-full bg-gray-50 grid place-items-center">
          <span class="font-bold text-lg">
            {{ currentPage }}
          </span>
        </div>
      </div>
      <button
        class="h-14 bg-gray-200 hover:bg-gray-300 flex items-center justify-center rounded-2xl text-13 tracking-wider font-bold uppercase cursor-pointer w-32 lg:w-32 bg-grey-6 text-grey-5"
        type="button"
        @click="goNext()"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import dummyData from '@/dummyData'
import IconPending from '@/components/icons/IconPending.vue'
import IconPaid from '@/components/icons/IconPaid.vue'
import SortMenu from '@/components/SortMenu.vue'

import { SortingOptions } from '@/enums'
import { onMounted, ref } from 'vue'

const paginatedData = ref([] as ListItem[])
const currentPage = ref(1 as number)

interface ListItem {
  project_id: number
  amount: number
  state: string
  schedule_date: string
}

const myArray: ListItem[] = dummyData
const totalItems = dummyData.length
const itemsPerPage = 8
const totalPages = calculateTotalPages(totalItems, itemsPerPage)

function uuid() {
  return crypto.randomUUID()
}

function goNext() {
  if (currentPage.value === totalPages) return
  currentPage.value = ++currentPage.value
  paginatedData.value = paginateArray<ListItem>(myArray, itemsPerPage, currentPage.value)
}

function goPrev() {
  if (currentPage.value === 1) return
  currentPage.value = --currentPage.value
  paginatedData.value = paginateArray<ListItem>(myArray, itemsPerPage, currentPage.value)
}

// Function to paginate an array of objects
function paginateArray<T>(array: T[], pageSize: number, currentPage: number): T[] {
  const startIndex = (currentPage - 1) * pageSize
  const endIndex = startIndex + pageSize
  return array.slice(startIndex, endIndex)
}

function calculateTotalPages(totalItems: number, itemsPerPage: number): number {
  return Math.ceil(totalItems / itemsPerPage)
}

function sortList(sortBy: SortingOptions) {
  if (sortBy === SortingOptions.Amount) {
    let sorted = myArray.sort(sortByAmount)
    paginatedData.value = paginateArray<ListItem>(sorted, itemsPerPage, currentPage.value)
  } else if (sortBy === SortingOptions.State) {
    let sorted = myArray.sort(sortByState)
    paginatedData.value = paginateArray<ListItem>(sorted, itemsPerPage, currentPage.value)
  } else if (sortBy === SortingOptions.ProjectId) {
    let sorted = myArray.sort(sortByProject)
    paginatedData.value = paginateArray<ListItem>(sorted, itemsPerPage, currentPage.value)
  } else if (sortBy === SortingOptions.Date) {
    let sorted = myArray.sort(sortByDate)
    paginatedData.value = paginateArray<ListItem>(sorted, itemsPerPage, currentPage.value)
  }
}

function sortByAmount(a: ListItem, b: ListItem): number {
  return a.amount - b.amount
}

function sortByState(a: ListItem, b: ListItem): number {
  return a.state.length - b.state.length
}

function sortByProject(a: ListItem, b: ListItem): number {
  return a.project_id - b.project_id
}

function sortByDate(a: ListItem, b: ListItem): number {
  const dateA = new Date(a.schedule_date).getTime()
  const dateB = new Date(b.schedule_date).getTime()

  return dateA - dateB
}

function handleItemSelect(op: SortingOptions) {
  sortList(op)
}

onMounted(() => {
  const paginatedArray = paginateArray<ListItem>(myArray, itemsPerPage, 1)
  paginatedData.value = paginatedArray
})
</script>
