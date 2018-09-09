<template>
  <div class="info">
    <div class="info__actions">
      <div class="search">
        <span class="search__label">Live table search</span>
        <input
          type="text"
          placeholder="Search here..."
          v-model="search"
          class="search__input"
        >
      </div>
      <div class="currency-sum">
        <span class="currency-sum__label">Currency sum: </span>
        <span class="currency-sum__value">{{ currencysum }}</span>
      </div>
    </div>
    <table
      class="table"
    >
      <tr>
        <th
          v-for="header in tableHeaders"
          :key="header.key"
          class="table__cell table__cell_header"
        >
          <div class="header__title">
            {{ header.label }}
          </div>
          <div
            class="header__sorting"
          >
            <span class="sorting__label">Sort by:</span>
            <select
              class="sorting__variants"
              v-model="sortingValues[header.key]"
              @change="onSortByColumn(header.key)"
            >
              <option value="default" disabled hidden>Not sorted</option>
              <option value="asc">Ascending</option>
              <option value="desc">Descending</option>
            </select>
          </div>
        </th>
      </tr>
      <tr
        v-if="filteredTable"
        v-for="(data, index) in filteredTable"
        :key="`${data.id}-${index}`"
        class="table__row"
      >
        <td class="table__cell">
          <span class="cell__value">
            {{ data.name }}
          </span>
          <div class="cell__actions">
            <button @click="onCellValueEdit(data.id, 'name')">
              Edit
            </button>
          </div>
        </td>
        <td class="table__cell">
          <span class="cell__value">
            {{ data.location }}
          </span>
          <div class="cell__actions">
            <button @click="onCellValueEdit(data.id, 'location')">
              Edit
            </button>
          </div>
        </td>
        <td class="table__cell">
          <span class="cell__value">
            {{ data.currency }}
          </span>
          <div class="cell__actions">
            <button @click="onCellValueEdit(data.id, 'currency')">
              Edit
            </button>
          </div>
        </td>
      </tr>
    </table>
    <div :class="popupClasses">
      <div class="popup__body">
        <input
          type="text"
          v-model="editPopup.value"
        >
        <button @click="onCellValueSave">
          Save
        </button>
        <button @click="onClosePopup">
          Cancel
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import tableData from '@/assets/data.json'

export default {
  name: 'Table',
  data () {
    return {
      tableData,
      tableHeaders: [
        {
          label: 'Name',
          key: 'name'
        },
        {
          label: 'Location',
          key: 'location'
        },
        {
          label: 'Currency',
          key: 'currency'
        }
      ],
      search: '',
      sortingValues: {
        name: 'default',
        location: 'default',
        currency: 'default'
      },
      editPopup: {
        isVisible: false,
        id: '',
        field: '',
        value: ''
      }
    }
  },
  computed: {
    currencysum () {
      return this.tableData.reduce((prev, curr) => {
        prev += parseInt(curr.currency)
        return prev
      }, 0)
    },
    filteredTable () {
      return [...this.searchInColumn('name'), ...this.searchInColumn('location'), ...this.searchInColumn('currency')]
    },
    isSearchSucceded () {
      return this.filteredTable.length > 0
    },
    popupClasses () {
      return [
        'popup',
        { 'active': this.editPopup.isVisible }
      ]
    }
  },
  methods: {
    searchInColumn (columnName) {
      return this.tableData.filter(data => data[columnName].toString().toLowerCase().includes(this.search.toLowerCase()))
    },
    onSortByColumn (columnKey) {
      const sortingResult = sortType => this.sortingValues[columnKey] === sortType ? 1 : -1
      this.filteredTable.sort((curr, next) => {
        if (curr[columnKey] < next[columnKey]) {
          return sortingResult('desc')
        } else if (curr[columnKey] > next[columnKey]) {
          return sortingResult('asc')
        } else {
          return 0
        }
      })
    },
    onCellValueEdit (cellId, columnKey) {
      this.onOpenPopup()
      const currentItem = this.tableData.find(dataItem => dataItem.id === cellId)
      this.editPopup.id = currentItem.id
      this.editPopup.field = columnKey
      this.editPopup.value = currentItem[columnKey]
    },
    onCellValueSave () {
      this.tableData.find(dataItem => dataItem.id === this.editPopup.id)[this.editPopup.field] = this.editPopup.value
      this.onClosePopup()
    },
    onOpenPopup () {
      this.editPopup.isVisible = true
    },
    onClosePopup () {
      this.editPopup.isVisible = false
    }
  }
}
</script>

<style scoped lang="scss">
  .info {
    padding: 16px;

    &__actions {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 16px;
    }

    .search {
      &__label {
        margin-right: 8px;
      }
    }
  }

  .table {
    width: 100%;
    border-collapse: collapse;

    &__cell {
      width: calc(100% / 3);
      padding: 16px;
      border: 1px solid #000;

      &_header {
        text-align: left;
      }
    }

    .header {
      &__title, &__sorting {
        display: inline-block;
        vertical-align: middle;
        position: relative;
      }

      &__sorting {
        float: right;

        .sorting__label {
          font-weight: normal;
          font-size: 14px;
          margin-right: 6px;
        }
      }
    }

    .cell {
      &__value {
        padding: 3px;
      }

      &__actions {
        float: right;
      }

      &__value, &__input {
        font-size: 16px;
        height: 24px;
      }

      &__value, &__input, &__actions {
        display: inline-block;
        vertical-align: middle;
        position: relative;
      }
    }
  }
</style>
