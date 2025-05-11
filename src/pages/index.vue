<template>
  <div class="pa-4">
    <!-- ПОИСК -->
    <InputWithSuggestions
      class="mb-2"
      label="Введите название фильма"
      :rules="inputRules"
      :suggestions="filmTitleList"
      @change-value="(val) => search = val"
    />

    <!-- ФИЛЬТРЫ -->
    <v-row>
      <v-col cols="4">
        <v-autocomplete
          v-model="filters.type"
          clearable
          color="primary"
          density="compact"
          :items="typeList"
          label="Тип"
          variant="outlined"
        />
      </v-col>

      <v-col cols="4">
        <v-autocomplete
          v-model="filters.rating"
          clearable
          color="primary"
          density="compact"
          item-title="name"
          item-value="value"
          :items="ratingList"
          label="Рейтинг"
          variant="outlined"
        />
      </v-col>

      <v-col cols="4">
        <v-autocomplete
          v-model="filters.year"
          clearable
          color="primary"
          density="compact"
          :items="yearList"
          label="Год"
          variant="outlined"
        />
      </v-col>
    </v-row>

    <!-- СОРТИРОВКА И ГРУППИРОВКА -->
    <v-row class="d-flex justify-end">
      <v-col cols="3">
        <v-autocomplete
          v-model="sortBy"
          clearable
          color="primary"
          density="compact"
          item-title="name"
          item-value="value"
          :items="sortByList"
          label="Сортировка"
          variant="outlined"
        />
      </v-col>

      <v-col cols="3">
        <v-autocomplete
          v-model="groupBy"
          clearable
          color="primary"
          density="compact"
          item-title="name"
          item-value="value"
          :items="groupByList"
          label="Группировка"
          variant="outlined"
        />
      </v-col>
    </v-row>

    <DataTable
      v-if="filmsList"
      :filter-function="filterFunction"
      :group-by="groupFunction"
      :items="filmsList"
      :search="search"
      :sort-by="sortFunction"
    />
  </div>
</template>

<script>
  import filmsList from '../../kinopoisk.json'
  import DataTable from '@/components/DataTable.vue'
  import InputWithSuggestions from '@/components/InputWithSuggestions.vue'

  export default {
    components: {
      DataTable,
      InputWithSuggestions,
    },

    data: () => ({
      sortBy: null,
      sortByList: [
        { name: 'По году', value: 'year' },
        { name: 'По рейтингу', value: 'rating.imdb' },
        { name: 'По хронометражу', value: 'movieLength' },
      ],
      groupBy: null,
      groupByList: [
        { name: 'По году', value: 'year' },
        { name: 'По типу', value: 'type' },
      ],
      filters: {
        type: null,
        rating: null,
        year: null,
      },
      ratingList: [
        { name: 'Больше 8', value: '>= 8' },
        { name: 'От 5 до 8', value: '5 - 8' },
        { name: 'Меньше 5', value: '< 5' },
      ],

      search: '',
      inputRules: [value => value.length >= 1 || 'Минимум 1 символ'],
    }),

    computed: {
      filmsList () {
        return filmsList.docs
      },

      filmTitleList () {
        return [...new Set(this.filmsList.map(item => item.name))]
      },

      typeList () {
        return [...new Set(this.filmsList.map(item => item.type))]
      },

      yearList () {
        const currentYear = new Date().getFullYear()
        return Array.from({ length: currentYear - 1950 + 1 }, (_, index) => currentYear - index)
      },
    },

    methods: {
      sortFunction () {
        return this.sortBy ? [{ key: this.sortBy, 'order': 'desc' }] : []
      },

      groupFunction () {
        return this.groupBy ? [{ key: this.groupBy }] : []
      },

      filterFunction (item) {
        const checkByType = this.filters.type ? item.type === this.filters.type : true
        const checkByYear = this.filters.year ? +item.year === +this.filters.year : true
        let checkByRating = true

        switch(this.filters.rating) {
          case '>= 8':
            checkByRating = +item.rating.imdb >= 8
            break
          case '5 - 8':
            checkByRating = +item.rating.imdb >= 5 && +item.rating.imdb < 8
            break
          case '< 5':
            checkByRating = +item.rating.imdb < 5
            break
          default:
            checkByRating = true
            break
        }

        return checkByType && checkByYear && checkByRating
      },
    },
  }
</script>
