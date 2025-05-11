<template>
  <div>
    <v-data-table
      density="compact"
      fixed-header
      :group-by="groupBy()"
      :headers="headers"
      height="700px"
      hide-default-footer
      hide-no-data
      item-key="id"
      :items="filmList"
      items-per-page="-1"
      :search="search"
      :sort-by="sortBy()"
    >
      <template #group-header="{ item, columns, toggleGroup, isGroupOpen }">
        <tr v-if="item.items.length > 1">
          <td :colspan="columns.length">
            <div class="d-flex align-center">
              <v-btn
                v-if="item.items.length > 1"
                color="medium-emphasis"
                density="comfortable"
                :icon="isGroupOpen(item) ? '$expand' : '$next'"
                size="small"
                variant="outlined"
                @click="toggleGroup(item)"
              />

              <span class="ms-4"><b>{{ item.value }}</b></span>
            </div>
          </td>
        </tr>

        <tr v-else>
          <td><b>{{ item.value }}</b></td>
          <td v-for="header in headers" :key="header.value">
            {{ item.items[0].columns[header.value] }}
          </td>
        </tr>
      </template>
    </v-data-table>

    <!--    loading-->
    <!--    :search="filter"-->
    <!--    show-expand-->
    <!--    :sort-by="sortBy"-->
    <!--    :sort-desc="sortDesc"-->
  </div>
</template>

<script>
  export default {
    name: 'DataTable',

    props: {
      items: {
        type: Array,
        required: true,
      },
      groupBy: {
        type: Function,
        required: false,
        default: () => [],
      },
      sortBy: {
        type: Function,
        required: false,
        default: () => [],
      },
      filterFunction: {
        type: Function,
        default: () => true,
      },
      search: String,
    },

    data: () => ({
      headers: [
        { title: 'Название', value: 'name' },
        { title: 'Райтинг', value: 'rating.imdb' },
        { title: 'Год', value: 'year' },
        { title: 'Хронометраж', value: 'movieLength' },
        { title: 'Тип', value: 'type' },
      ],
    }),

    computed: {
      filmList () {
        return this.items.filter(this.filterFunction)
      },
    },
  }
</script>
