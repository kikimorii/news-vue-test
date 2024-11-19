<script setup>
import { ref, defineProps, defineEmits } from 'vue';

const props = defineProps({
  nodes: Array,
  categories: Array,
  targets: Array,
  tags: Array,
});

const emit = defineEmits(['search']);

const searchText = ref('');
const startDate = ref('');
const endDate = ref('');
const selectedNode = ref(null);
const selectedCategory = ref(null);
const selectedTarget = ref(null);
const selectedTag = ref(null);

const handleSearch = () => {
  emit('search', {
    searchText: searchText.value,
    startDate: startDate.value ? formatDate(startDate.value) : null,
    endDate: endDate.value ? formatDate(endDate.value) : null,
    selectedNode: selectedNode.value,
    selectedCategory: selectedCategory.value,
    selectedTarget: selectedTarget.value,
    selectedTag: selectedTag.value,
  });
};

// Функция форматирования даты в формате YYYY-MM-DD
const formatDate = (date) => {
  const d = new Date(date);
  const year = d.getFullYear();
  const month = String(d.getMonth() + 1).padStart(2, '0'); // Месяцы начинаются с 0
  const day = String(d.getDate()).padStart(2, '0');
  return `${year}-${month}-${day}`;
};

// Функция сброса фильтров
const resetFilters = () => {
  searchText.value = '';
  startDate.value = '';
  endDate.value = '';
  selectedNode.value = null;
  selectedCategory.value = null;
  selectedTarget.value = null;
  selectedTag.value = null;
};
</script>

<template>
  <form class="col-auto col-md-12" @submit.prevent="handleSearch">
    <button
      class="btn_filter d-md-none"
      type="button"
      data-bs-toggle="offcanvas"
      data-bs-target="#offcanvasBottom"
      aria-controls="offcanvasBottom"
    ></button>

    <div class="form_wrapper d-none d-md-block">
      <div class="row align-items-center">
        <div class="col-6">
          <div class="g-filter input_wrapper input_text-wrapper d-flex align-items-center">
            <input
              type="text"
              id="news-search"
              class="form-control g-filter"
              placeholder="Текст для поиска"
              v-model="searchText"
            />
          </div>
        </div>
        <div class="col-2">
          <div class="g-filter input_wrapper input_data start">
            <input type="date" class="form-control g-filter" v-model="startDate" />
          </div>
        </div>
        <div class="col-2">
          <div class="g-filter input_wrapper input_data end">
            <input type="date" class="form-control g-filter" v-model="endDate" />
          </div>
        </div>
        <div class="col-2 d-flex justify-content-around justify-content-xxl-between gap-1">
          <button type="button" class="btn-g-16 g-secondary" @click="handleSearch">Найти</button>
          <button type="button" class="btn_clean" @click="resetFilters" style="font-size: 12px">
            <span class="btn_clean-text d-none d-xxl-inline">Сбросить</span>
            <div class="btn_clean-icon">
              <img class="btn_clean-def" src="/img/clean-btn.svg" alt="clean" />
              <div class="btn_clean-hover"></div>
            </div>
          </button>
        </div>
      </div>
      <div class="row mt-2">
        <div class="col">
          <div class="select_wrapper g-filter d-flex align-items-center">
            <select v-model="selectedNode" class="form-select g-filter">
              <option class="g-filter-select-def" value="">Узел ({{ nodes.length }})</option>
              <option
                v-for="node in nodes"
                :key="node.id"
                :value="node.id"
                class="g-filter-select"
              >
                {{ node.title }}
              </option>
            </select>
          </div>
        </div>
        <div class="col">
          <div class="select_wrapper g-filter d-flex align-items-center">
            <select v-model="selectedCategory" class="form-select g-filter">
              <option class="g-filter-select-def" value="">Рубрика ({{ categories.length }})</option>
              <option
                v-for="category in categories"
                :key="category.id"
                :value="category.id"
                class="g-filter-select"
              >
                {{ category.title }}
              </option>
            </select>
          </div>
        </div>
        <div class="col">
          <div class="select_wrapper g-filter d-flex align-items-center">
            <select v-model="selectedTarget" class="form-select g-filter">
              <option class="g-filter-select-def" value="">Участники ({{ targets.length }})</option>
              <option
                v-for="target in targets"
                :key="target.id"
                :value="target.id"
                class="g-filter-select"
              >
                {{ target.title }}
              </option>
            </select>
          </div>
        </div>
        <div class="col">
          <div class="select_wrapper g-filter d-flex align-items-center">
            <select v-model="selectedTag" class="form-select g-filter">
              <option class="g-filter-select-def" value="">Теги ({{ tags.length }})</option>
              <option
                v-for="tag in tags"
                :key="tag.id"
                :value="tag.id"
                class="g-filter-select"
              >
                {{ tag.title }}
              </option>
            </select>
          </div>
        </div>
      </div>
    </div>

    <div
      class="offcanvas offcanvas-bottom h-auto rounded-top-4"
      tabindex="-1"
      id="offcanvasBottom"
      aria-labelledby="offcanvasBottomLabel"
    >
      <div class="offcanvas-header">
        <h5 class="offcanvas-title" id="offcanvasBottomLabel">
          Поиск и фильтры
        </h5>
        <button
          type="button"
          class="btn-close text-reset"
          data-bs-dismiss="offcanvas"
          aria-label="Close"
        ></button>
      </div>
      <div class="offcanvas-body">
        <div class="form_wrapper row gap-3 pb-4">
          <div class="col-12">
            <div class="g-filter input_wrapper input_text-wrapper d-flex align-items-center">
              <input
                type="text"
                id="news-search-offcanvas"
                class="form-control g-filter"
                placeholder="Текст для поиска"
                v-model="searchText"
              />
            </div>
          </div>
          <div class="col-12 d-flex gap-2">
            <div class="col">
              <div class="g-filter input_wrapper input_data start">
                <input type="date" class="form-control g-filter" v-model="startDate" />
              </div>
            </div>
            <div class="col">
              <div class="g-filter input_wrapper input_data end">
                <input type="date" class="form-control g-filter" v-model="endDate" />
              </div>
            </div>
          </div>
          <div class="row mt-2">
            <div class="col-12">
              <div class="select_wrapper g-filter d-flex align-items-center">
                <select v-model="selectedNode" class="form-select g-filter">
                  <option class="g-filter-select-def" value="">Узел ({{ nodes.length }})</option>
                  <option
                    v-for="node in nodes"
                    :key="node.id"
                    :value="node.id"
                    class="g-filter-select"
                  >
                    {{ node.title }}
                  </option>
                </select>
              </div>
            </div>
            <div class="col-12">
              <div class="select_wrapper g-filter d-flex align-items-center">
                <select v-model="selectedCategory" class="form-select g-filter">
                  <option class="g-filter-select-def" value="">Рубрика ({{ categories.length }})</option>
                  <option
                    v-for="category in categories"
                    :key="category.id"
                    :value="category.id"
                    class="g-filter-select"
                  >
                    {{ category.title }}
                  </option>
                </select>
              </div>
            </div>
            <div class="col-12">
              <div class="select_wrapper g-filter d-flex align-items-center">
                <select v-model="selectedTarget" class="form-select g-filter">
                  <option class="g-filter-select-def" value="">Участники ({{ targets.length }})</option>
                  <option
                    v-for="target in targets"
                    :key="target.id"
                    :value="target.id"
                    class="g-filter-select"
                  >
                    {{ target.title }}
                  </option>
                </select>
              </div>
            </div>
            <div class="col-12">
              <div class="select_wrapper g-filter d-flex align-items-center">
                <select v-model="selectedTag" class="form-select g-filter">
                  <option class="g-filter-select-def" value="">Теги ({{ tags.length }})</option>
                  <option
                    v-for="tag in tags"
                    :key="tag.id"
                    :value="tag.id"
                    class="g-filter-select"
                  >
                    {{ tag.title }}
                  </option>
                </select>
              </div>
            </div>
          </div>
          <div class="col-12 d-flex align-items-center justify-content-between">
            <div class="col-auto">
              <button type="button" class="btn-g-16 g-secondary" @click="handleSearch">Найти</button>
            </div>
            <div class="col-auto">
              <button type="button" class="btn_clean" @click="resetFilters">
                Сбросить
                <div class="btn_clean-icon">
                  <img class="btn_clean-def" src="/img/clean-btn.svg" alt="clean" />
                  <div class="btn_clean-hover"></div>
                </div>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </form>
</template>