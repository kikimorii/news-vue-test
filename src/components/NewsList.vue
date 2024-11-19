<script setup>
    import NewsCard from './NewsCard.vue'
    defineProps({
        items: Array
    })
</script>

<template>
    <NewsCard
        v-for="item in items"
        :key="item.pubId"
        :imageUrl="`https://media.guap.ru/${item.mediaCatalog}/_title.jpg?s=xl`"
        :title="item.title"
        :description="item.abstract"
        :date="item.date"
        :pageLink="`https://guap.ru/n/dev/samples/news/pub?id=${item.id}`"
    />
</template>