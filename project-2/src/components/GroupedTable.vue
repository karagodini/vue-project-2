<template>
    <div>
      <input v-model="searchQuery" placeholder="Фильтр..." class="search" />
  
      <table class="grouped-table">
        <thead>
          <tr>
            <th v-for="key in visibleKeys" :key="key" @click="sortBy(key)">
              {{ key }}
              <span>{{ sortKey === key ? (sortAsc ? '▲' : '▼') : '' }}</span>
            </th>
          </tr>
        </thead>
  
        <tbody>
          <template v-for="(groupItems, groupKey) in groupedAndFiltered">
            <tr
              v-if="shouldGroup"
              :key="'group-' + groupKey"
              class="group-header"
              @click="toggleGroup(groupKey)"
              :style="{ cursor: groupItems.length > 1 ? 'pointer' : 'default' }"
            >
              <td :colspan="visibleKeys.length">
                <strong>{{ groupKey }}</strong>
                <span v-if="groupItems.length > 1">
                  ({{ collapsedGroups[groupKey] ? '+' : '-' }})
                </span>
              </td>
            </tr>
  
            <tr
              v-for="item in (!collapsedGroups[groupKey] || groupItems.length === 1 ? groupItems : [])"
              :key="item.id"
            >
              <td v-for="key in visibleKeys" :key="key">{{ item[key] }}</td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    name: 'GroupedTable',
    props: {
      items: {
        type: Array,
        required: true
      },
      groupBy: {
        type: Function,
        required: true
      },
      filter: {
        type: Function,
        default: () => () => true
      },
      sortFunction: {
        type: Function,
        default: null
      }
    },
    data() {
      return {
        collapsedGroups: {},
        searchQuery: '',
        sortKey: null,
        sortAsc: true
      };
    },
    computed: {
      shouldGroup() {
        return typeof this.groupBy === 'function';
      },
      visibleKeys() {
        return this.items.length > 0 ? Object.keys(this.items[0]) : [];
      },
      groupedAndFiltered() {
        const groups = {};
  
        this.items
          .filter(item => this.filter(item, this.searchQuery))
          .forEach(item => {
            const key = this.groupBy(item);
            if (!groups[key]) {
              groups[key] = [];
            }
            groups[key].push(item);
          });
  
        const sortedGroups = {};
        for (const key in groups) {
          let sorted = [...groups[key]];
          if (this.sortKey) {
            sorted.sort((a, b) => {
              const aVal = a[this.sortKey];
              const bVal = b[this.sortKey];
              return this.sortAsc
                ? aVal > bVal ? 1 : -1
                : aVal < bVal ? 1 : -1;
            });
          }
          sortedGroups[key] = sorted;
        }
  
        return sortedGroups;
      }
    },
    methods: {
      toggleGroup(key) {
        this.collapsedGroups[key] = !this.collapsedGroups[key];
      },
      sortBy(key) {
        if (this.sortKey === key) {
          this.sortAsc = !this.sortAsc;
        } else {
          this.sortKey = key;
          this.sortAsc = true;
        }
      }
    }
  };
  </script>
  
  <style scoped>
  .grouped-table {
    width: 100%;
    border-collapse: collapse;
  }
  
  .grouped-table th,
  .grouped-table td {
    border: 1px solid #ccc;
    padding: 8px;
    text-align: left;
  }
  
  .group-header {
    background-color: #f0f0f0;
  }
  
  .search {
    margin-bottom: 12px;
    padding: 6px 10px;
    width: 100%;
    box-sizing: border-box;
  }
  </style>
  