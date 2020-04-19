<template>
  <div id="app">
    <div class="l-container-4">
      <h1 class="u-text-center">{{ title }}</h1>

      <div class="courses u-mt-30">
        <div>
          <label>
            <input type="checkbox" v-model="showPrices" />
            Показывать цены
          </label>
        </div>
        <form class="courses-form form" role="form">
          <div>
            <select id="subj" name="subj" v-model="selectedSubject">
              <option value>Все предметы</option>
              <option v-for="item in subjects" :key="item">{{ item }}</option>
            </select>
          </div>
          <div>
            <select id="genre" name="genre" v-model="selectedGenre">
              <option value>Все жанры</option>
              <option v-for="item in genres" :key="item">{{ item }}</option>
            </select>
          </div>
          <div>
            <select id="grade" name="grade" v-model="selectedClass">
              <option value>Все классы</option>
              <option v-for="item in classes" :key="item">{{ item }}</option>
            </select>
          </div>
          <div>
            <input type="text" placeholder="Поиск" name="search" v-model="searchQuery" />
            <button class="courses-form-search-btn" type="submit" title="Найти"></button>
          </div>
        </form>
      </div>

      <!-- СПИСОК КУРСОВ -->
      <ul class="courses-list">
        <li class="courses-sci" v-for="item in filteredItems" :key="item.courseId">
          <div class="sci-figure">
            <img :src="`/svc/coursecover/${item.courseId}`" :alt="item.title" />
          </div>
          <div class="sci-info">
            <!-- {{ item }} -->
            <p class="sci-title">{{ item.subject }}</p>
            <p class="sci-grade">{{ getGrades(item.grade) }}</p>
            <p class="sci-genre">{{ item.genre }}</p>

            <p class="sci-meta">
              <a :href="`/offer/${item.courseId}`">Подробнее</a>
            </p>
            <p class="sci-controls">
              <a
                href="#"
                @click.prevent="tryCourse(item)"
                class="pure-button pure-button-primary btn-fluid"
              >
                {{ showPrices ? `${item.price} рублей` : 'Попробовать' }}
              </a>
            </p>
          </div>
        </li>
      </ul>
      <!--  -->
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data: () => ({
    items: [],

    showPrices: false,

    searchQuery: '',

    selectedSubject: '',
    subjects: [
      'Алгебра',
      'Английский язык',
      'Биология',
      'География',
      'Геометрия',
      'Демо-версия',
      'Информатика',
      'История',
      'Литература',
      'Математика',
      'Обществознание',
      'Окружающий мир',
      'Робототехника',
      'Русский язык',
      'Физика',
      'Химия'
    ],

    selectedGenre: '',
    genres: ['Демо', 'Задачник', 'Подготовка к ВПР', 'Подготовка к ЕГЭ', 'Рабочая тетрадь'],

    selectedClass: '',
    classes: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
  }),
  computed: {
    title() {
      return this.loading ? 'Витрина загружается' : 'Витрина'
    },
    filteredItems() {
      let items = this.items

      if (this.selectedSubject) {
        items = items.filter(item => item.subject === this.selectedSubject)
      }

      if (this.selectedGenre) {
        items = items.filter(item => item.genre === this.selectedGenre)
      }

      if (this.selectedClass) {
        items = items.filter(item => item.grade.includes(this.selectedClass))
      }

      if (this.searchQuery) {
        items = items.filter(item => item.title.search(new RegExp(this.searchQuery, 'i')) !== -1)
      }

      return items
    }
  },
  mounted() {
    this.loadItems()
  },
  methods: {
    loadItems() {
      this.loading = true

      axios
        .post('http://krapipl.imumk.ru:8082/api/mobilev1/update')
        .then(response => response.data)
        .then(response => (this.items = response.items))
        .catch(error => console.error(error))
        .finally(() => (this.loading = false))
    },
    tryCourse(item) {
      alert('Переход на страницу курса: ' + item.title)
    },
    getGrades(grade) {
      if (!grade.includes(';')) {
        return `${grade} класс`
      }

      const grades = grade.split(';')
      const firstGrade = grades[0]
      const lastGrade = grades[grades.length - 1]

      return `${firstGrade} - ${lastGrade} классы`
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
