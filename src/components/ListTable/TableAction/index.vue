<template>
  <div class="table-header">
    <slot name="header">
      <div class="table-header-left-side">
        <LeftSide v-if="hasLeftActions" :selected-rows="selectedRows" :table-url="tableUrl" v-bind="$attrs" v-on="$listeners" />
        <span v-else style="display: flex;flex-direction: row">
          <AutoDataSearch v-if="hasSearch" class="right-side-item action-search" v-bind="iSearchTableConfig" @tagSearch="handleTagSearch" />
          <DatetimeRangePicker v-if="hasDatePicker" v-bind="datePicker" class="datepicker" @dateChange="handleDateChange" />
        </span>
      </div>
      <div class="table-action-right-side">
        <AutoDataSearch v-if="hasLeftActions && hasSearch" class="right-side-item action-search" v-bind="iSearchTableConfig" @tagSearch="handleTagSearch" />
        <DatetimeRangePicker v-if="hasDatePicker && hasLeftActions" v-bind="datePicker" class="datepicker" @dateChange="handleDateChange" />
        <RightSide v-if="hasRightActions" :selected-rows="selectedRows" :table-url="tableUrl" v-bind="$attrs" v-on="$listeners" />
      </div>
    </slot>
  </div>
</template>

<script>
import AutoDataSearch from '@/components/AutoDataSearch'
import LeftSide from './LeftSide'
import DatetimeRangePicker from '@/components/FormFields/DatetimeRangePicker'
import RightSide from './RightSide'

const defaultTrue = { type: Boolean, default: true }
const defaultFalse = { type: Boolean, default: false }
export default {
  name: 'TableAction',
  components: {
    AutoDataSearch,
    LeftSide,
    DatetimeRangePicker,
    RightSide
  },
  props: {
    hasLeftActions: defaultTrue,
    hasSearch: defaultTrue,
    hasRightActions: defaultTrue,
    hasDatePicker: defaultFalse,
    datePicker: {
      type: Object,
      default: () => ({})
    },
    searchConfig: {
      type: Object,
      default: () => ({})
    },
    tableUrl: {
      type: String,
      default: ''
    },
    datePick: {
      type: Function,
      default: (val) => {}
    },
    searchTable: {
      type: Function,
      default: (val) => {}
    },
    selectedRows: {
      type: Array,
      default: () => []
    },
    tableColumns: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      keyword: ''
    }
  },
  computed: {
    hasSelectedRows() {
      return this.selectedRows.length > 0
    },
    iSearchTableConfig() {
      const keys = this.checkInTableColumns()
      const configDefault = {
        url: this.tableUrl,
        default: {
          ...this.searchConfig?.default,
          ...keys
        }
      }
      return Object.assign(configDefault)
    }
  },
  methods: {
    handleTagSearch(val) {
      this.searchTable(val)
    },
    handleDateChange(val) {
      this.datePick(val)
    },
    // 判断url中的查询条件
    checkInTableColumns() {
      const q = this.$route.query
      const routeQueryKeys = Object.keys(q)
      const keys = {}
      if (routeQueryKeys.length > 0) {
        routeQueryKeys.forEach(i => {
          this.tableColumns.forEach(k => {
            if (i === k.prop || i === k) {
              keys[i] = {
                key: i,
                label: k.label || '',
                value: decodeURI(q[i])
              }
            }
          })
        })
      }
      return keys
    }
  }
}
</script>

<style scoped>
  .table-header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }

  .right-side-item {
  }

  .right-side-actions >>> .el-button {
    border: none;
    padding: 5px;
    font-size: 14px;
    width: 26px;
    height: 26px;
    color: #888;
    background-color: transparent;
  }

  .right-side-actions >>> .fa {
    height: 16px;
    width: 16px;
  }

  .right-side-actions >>> .el-button:hover {
    background-color: rgb(0, 0, 0, 0.05);
  }

  .action-search >>> .el-input__suffix i {
    font-weight: 500;
    color: #888;
  }

  .right-side-actions {
    display: flex;
    padding-left: 10px;
    align-items: center;
    justify-content:center;
  }

  .table-action-right-side {
    display: flex;
    justify-content:center;
  }

  .export-item {
    display: block;
    padding: 5px 20px;
  }
  .datepicker{
    margin-left: 10px;
  }

</style>
