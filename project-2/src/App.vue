<template>
  <GroupedTable
    :items="items"
    :groupBy="groupBy"
    :filter="filter"
  />
</template>

<script>
import GroupedTable from './components/GroupedTable.vue';

export default {
  components: { GroupedTable },
  data() {
    return {
      items: [
        { id: 1, name: 'Иван', age: 25, city: 'Москва' },
        { id: 2, name: 'Мария', age: 30, city: 'Москва' },
        { id: 3, name: 'Петр', age: 22, city: 'Сочи' },
        { id: 4, name: 'Анна', age: 27, city: 'Сочи' },
        { id: 5, name: 'Вася', age: 24, city: 'Казань' }
      ]
    };
  },
  methods: {
    groupBy(item) {
      return item.city;
    },
    filter(item, search) {
      const term = search.toLowerCase();
      return (
        item.name.toLowerCase().includes(term) ||
        item.city.toLowerCase().includes(term)
      );
    }
  }
};
</script>
