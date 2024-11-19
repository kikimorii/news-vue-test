<template>
  <BlueimpGalleryInterface />
  <Header />

  <main class="main" id="main">
    <MenuDesktop />

    <div class="container-xxl" id="contentPage">
      <Breadcrumb />
      <div
        class="row flex-row flex-md-column align-items-center justify-content-between justify-content-md-start align-items-md-start mb-3"
      >
        <h1 class="col-auto">Новости</h1>
        <FilterForm 
          :nodes="nodes" 
          :categories="categories" 
          :targets="targets" 
          :tags="tags"
          @search="handleSearch"
        />
      </div>
      <NewsList :items="items" />
      
      <nav v-if="pagination" aria-label="Page navigation">
        <ul class="pagination align-items-center justify-content-center">
          <li class="page-item" :class="{ 'disabled': isFirstPageActive }">
            <a class="page-link" href="#" aria-label="Previous" @click.prevent="goToPage(currentPage - 1)">
              <span aria-hidden="true"><i class="bi bi-chevron-left"></i></span>
            </a>
          </li>

          <li 
            v-for="pageNum in pagination.endPage - pagination.startPage + 1" 
            :key="pagination.startPage + pageNum - 1" 
            class="page-item"
          >
            <a 
              class="page-link" 
              :class="{ 'active': currentPage === (pagination.startPage + pageNum - 1) }" 
              href="#"
              @click.prevent="goToPage(pagination.startPage + pageNum - 1)"
            >
              {{ pagination.startPage + pageNum - 1 }}
            </a>
          </li>

          <li class="page-item" :class="{ 'disabled': isLastPageActive }">
            <a class="page-link" href="#" aria-label="Next" @click.prevent="goToPage(currentPage + 1)">
              <span aria-hidden="true"><i class="bi bi-chevron-right"></i></span>
            </a>
          </li>
        </ul>
      </nav>
    </div>
  </main>

  <Footer />
</template>

<script setup>
import { onMounted, ref, computed } from 'vue';
import axios from 'axios';
import BlueimpGalleryInterface from './components/BlueimpGalleryInterface.vue';
import Header from './components/Header.vue';
import MenuDesktop from './components/MenuDesktop.vue';
import Breadcrumb from './components/Breadcrumb.vue';
import FilterForm from './components/FilterForm.vue';
import NewsList from './components/NewsList.vue';
import Footer from './components/Footer.vue';

// Массивы для хранения данных
const nodes = ref([]);
const categories = ref([]);
const targets = ref([]);
const tags = ref([]);
const items = ref([]);
const pagination = ref(null);
const currentPage = ref(1);
const filters = ref({}); // Добавляем объект для хранения фильтров

// Функция для получения данных с api
const fetchNews = async (pageNum, filters = {}) => {
  try {
    // Создаем массив параметров
    const params = new URLSearchParams();
    if (filters.selectedNode) params.append('nodesIds', filters.selectedNode);
    if (filters.selectedCategory) params.append('categoriesIds', filters.selectedCategory);
    if (filters.selectedTarget) params.append('targetsIds', filters.selectedTarget);
    if (filters.selectedTag) params.append('tagsIds', filters.selectedTag);
    if (filters.startDate) params.append('begin', filters.startDate);
    if (filters.endDate) params.append('end', filters.endDate);
    
    // Добавляем параметры для пагинации
    params.append('page', pageNum);
    params.append('itemsOnPage', 10);

    // Делаем запрос с параметрами
    const { data } = await axios.get(`https://api.guap.ru/news/api/v2/get-list-pubs?${params.toString()}`);
    items.value = data.items;
    pagination.value = data.pagination || {};
  } catch (err) {
    console.log(err);
  }
};

// Метод для перехода на новую страницу
const goToPage = (newPage) => {
  if (newPage < 1 || newPage > pagination.value.endPage) return; // Проверяем границы
  currentPage.value = newPage;

  // Обновляем хеш URL только с непустыми параметрами
  const filterParams = new URLSearchParams();
  if (filters.value.selectedNode) filterParams.append('nodesIds', filters.value.selectedNode);
  if (filters.value.selectedCategory) filterParams.append('categoriesIds', filters.value.selectedCategory);
  if (filters.value.selectedTarget) filterParams.append('targetsIds', filters.value.selectedTarget);
  if (filters.value.selectedTag) filterParams.append('tagsIds', filters.value.selectedTag);
  if (filters.value.startDate) filterParams.append('begin', filters.value.startDate);
  if (filters.value.endDate) filterParams.append('end', filters.value.endDate);

  // Добавляем номер страницы
  filterParams.append('page', newPage);
  
  // Обновляем хеш URL
  window.location.hash = `#${filterParams.toString()}`;
  fetchNews(newPage, filters.value); // Загружаем данные для новой страницы
  window.scrollTo({ top: 0, behavior: 'smooth' });
};

// Проверяем хеш при загрузке компонента
const checkHash = () => {
  const hash = window.location.hash;
  const params = new URLSearchParams(hash.slice(1));
  
  // Получаем номер страницы
  const page = params.get('page');
  if (page) {
    currentPage.value = Number(page);
  }

  // Получаем параметры фильтров из хеша, игнорируя null значения
  filters.value.selectedNode = params.get('nodesIds') || null;
  filters.value.selectedCategory = params.get('categoriesIds') || null;
  filters.value.selectedTarget = params.get('targetsIds') || null;
  filters.value.selectedTag = params.get('tagsIds') || null;
  filters.value.startDate = params.get('begin') || null;
  filters.value.endDate = params.get('end') || null;
};

// Функция для получения данных из нескольких API
const fetchAllData = async () => {
  const endpoints = [
    'https://api.guap.ru/news/api/v2/get-reg-nodes',
    'https://api.guap.ru/news/api/v2/get-reg-categories',
    'https://api.guap.ru/news/api/v2/get-reg-targets',
    'https://api.guap.ru/news/api/v2/get-reg-tags'
  ];

  try {
    const responses = await Promise.all(endpoints.map(endpoint => axios.get(endpoint)));

    // Собираем данные в отдельные массивы
    nodes.value = responses[0].data.map(item => ({
      id: item.id,
      title: item.title
    }));
    categories.value = responses[1].data.map(item => ({
      id: item.id,
      title: item.title
    }));
    targets.value = responses[2].data.map(item => ({
      id: item.id,
      title: item.title
    }));
    tags.value = responses[3].data.map(item => ({
      id: item.id,
      title: item.title
    }));
  } catch (err) {
    console.error('Ошибка при получении данных:', err);
  }
};

const handleSearch = (newFilters) => {
  console.log('Фильтры поиска:', newFilters);
  filters.value = newFilters; // Обновляем фильтры
  goToPage(1); // Перейти на первую страницу с новыми фильтрами
};

onMounted(() => {
  checkHash(); // Проверяем хеш при загрузке
  fetchNews(currentPage.value, filters.value); // Загружаем данные на основании текущей страницы и фильтров
  fetchAllData(); // Получаем данные из нескольких API
});

const isFirstPageActive = computed(() => pagination.value?.activeFirstPage || false);
const isLastPageActive = computed(() => pagination.value?.activeLastPage || false);
</script>
