<script setup lang="ts">
import { onMounted, ref, computed } from 'vue'
import type { Ref } from 'vue'

import PaginationComponent from '@/components/PaginationComp.vue'
import SearchComponent from '@/components/SearchComp.vue'

interface List {
  id: number
  title: string
  description: string
}

const list: Ref<List[]> = ref([])
const page: Ref<number> = ref(1)
const allPages: Ref<number> = ref(0)
const search: Ref<string> = ref('')

const getList = async () => {
  list.value = []
  let res = await fetch(`https://api.test-webest.ru/list/?page=${page.value}`)
  let data = await res.json()
  allPages.value = data.page_count
  list.value = data.data
  list.value.length = 30
}

const searchElement = computed(() => {
  return list.value.filter((el) => {
    return el.title.includes(search.value)
  })
})

const setPage = (pageNumber: number) => {
  page.value = pageNumber
  getList()
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}

onMounted(async () => {
  await getList()
})
</script>
<template>
  <main class="main">
    <section class="main-page">
      <div class="container">
        <div class="search-wrapper">
          <SearchComponent v-model:input="search" />
        </div>
        <TransitionGroup name="list" tag="ul" class="tenders-list">
          <router-link class="tender-link" :to="{ name: 'element', query: { id: element.id } }"
            v-for="element in searchElement" :key="element.id">
            <li class="tender-item">
              <h3 class="tender-item__title">{{ element.title }}</h3>
              <p class="tender-item__text">{{ element.description }}</p>
            </li>
          </router-link>
        </TransitionGroup>
        <PaginationComponent :allPages="allPages" @set="setPage($event)" @go="setPage($event + page)" :page="page" />
      </div>
    </section>
  </main>
</template>

<style lang="scss">
.search-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
}

.tenders-list {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  margin: 50px 0;
}

.tender-item {
  color: rgb(40, 40, 44);
  background-color: rgb(243, 243, 246);
  padding: 10px;
  border-radius: 10px;
  height: 210px;
  list-style: none;

  &__title {
    font-size: 16px;
    line-height: 110%;
    margin-bottom: 16px;
  }

  &__text {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    line-clamp: 5;
    -webkit-line-clamp: 5;
    -webkit-box-orient: vertical;
    font-size: 16px;
    line-height: 20px;
  }
}

.tender-link {
  width: 240px;
}

.list-enter-active,
.list-leave-active {
  transition: all 1s ease;
  transition-duration: 1s;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transition-duration: 1s;
  transform: translateX(30px);
}
</style>