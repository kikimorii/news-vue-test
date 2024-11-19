<script setup>
import { computed } from 'vue'

// Определение свойств
const props = defineProps({
  date: String,
  imageUrl: String,
  title: String,
  description: String,
  pageLink: String,
})

// Функция для форматирования даты
const formatDate = isoString => {
  const date = new Date(isoString)

  // Получаем отдельные компоненты даты
  const year = date.getFullYear()
  const month = date.toLocaleString('default', { month: 'long' }) // Название месяца
  const day = String(date.getDate()).padStart(2, '0') // Дата с нулем спереди
  const hours = String(date.getHours()).padStart(2, '0') // Часы с нулем спереди
  const minutes = String(date.getMinutes()).padStart(2, '0') // Минуты с нулем спереди
  const seconds = String(date.getSeconds()).padStart(2, '0') // Секунды с нулем спереди

  // Возвращаем массив строк для дальнейшего объединения
  return [day, month, year]
}

// Вычисляемое свойство для форматированной даты
const formattedDate = computed(() => {
  return formatDate(props.date)
})
</script>

<template>
  <div class="row border-bottom mb-3">
    <a
      :href="pageLink"
      class="d-flex flex-column flex-md-row col-12 col-xl-10 insert-custom-row news_block-content"
    >
      <div class="col-md-2 order-2 order-md-1">
        <div class="col-sm-3 col-lg-2 pubs-data d-flex gap-1 mb-2">
          <span class="day allnews">{{ formattedDate[0] }}</span>
          <div class="month-year allnews">
            {{ formattedDate[1] }}
            <br class="d-none d-md-block" />
            {{ formattedDate[2] }}
          </div>
        </div>
      </div>
      <div class="col-md-3 order-1 order-md-2 mb-3">
        <img class="rounded w-100" :src="imageUrl" alt="картинка" />
      </div>
      <div class="col-md-7 order-3">
        <h5>{{ title }}</h5>
        <p>{{ description }}</p>
      </div>
    </a>
    <div class="d-none d-xl-block col-2">
      <a href="#" class="badge badge-g-sm">ГУАП</a>
      <a href="#" class="badge badge-g-sm">Институт 1</a>
      <a href="#" class="badge badge-g-sm">Кафедра 14</a>
      <a href="#" class="badge badge-g-sm">Приемная кампания</a>
      <a href="#" class="badge badge-g-sm">Поступающие</a>
      <a href="#" class="badge badge-g-sm">Профориентация (школы)</a>
      <a href="#" class="badge badge-g-sm">Школьники</a>
    </div>
  </div>
</template>
