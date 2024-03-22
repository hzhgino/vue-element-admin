
<template>
  <div class="components-container">
    <h1>TikTok数据采集</h1>
    <!-- 动态表单 -->
    <el-table :data="tableData" border style="width: 100%" @row-click="rowClick">
      <el-table-column label="选择" width="55" align="center">
        <template slot-scope="scope">
          <el-radio v-model="selectRowId" :label="scope.row.id">
            {{ '' }}
          </el-radio>
        </template>
      </el-table-column>
      <el-table-column
        v-for="item in tableColumnData"
        :key="item.key"
        :label="item.name"
        :prop="item.key"
        :width="cWidth(item.key)"
      />

    </el-table>
    <!-- 动态表单  end -->
    <!-- 翻页组件 -->
    <el-pagination
      :current-page="currentPage"
      :page-sizes="[10, 20, 30, 40]"
      :page-size="pageSize"
      layout="total, prev, pager, next, jumper"
      :total="total"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    />
  </div>
</template>

<script>

import {getPage} from '@/api/tiktok-index'

export default {
  name: 'TiktokVideoTable',
  components: {
  },
  data() {
    return {
      // 表头数组
      tableColumnData: [],
      // 列表数据数据
      tableData: [],
      // 每页显示条数
      pageSize: 10,
      // 列表数据总条数
      total: 0,
      // 当前页码
      currentPage: 1,
      // 列表搜索条件
      queryParams: { 'keywork': '' },

      // 选中行 id
      selectRowId: ''

    }
  },
  mounted() {
    this.loadPage(this.currentPage)
  },
  beforeDestroy() {
    // 组件销毁时，清除定时器
  },
  methods: {
    // 设置表单列宽
    cWidth(colKey) {
      if (colKey === 'videoDesc') {
        return 800
      }
    },

    rowClick(row) {
      // 选中行
      this.selectRowId = row.id
    },

    // 加载翻页数据
    // page：页码
    loadPage(page) {
      getPage(page, this.pageSize, this.queryParams).then(res => {
        if (res.code === 200) {
          // 数据总数
          this.total = res.count
          // 当前页码
          this.currentPage = res.current
          const respData = res.data[0]
          // 表头数据（过滤不显示的字段）
          this.tableColumnData = respData.tableColumn.filter(item => !item.hiddenColumn)
          // 表格数据
          this.tableData = respData.tableData
        }
      })
    },
    //   currentPage 改变时会触发
    handleCurrentChange(val) {
      this.loadPage(val)
    },
    // pageSize 改变时会触发
    handleSizeChange(val) {
      this.loadPage(val)
    }
  }
}
</script>

<style>
.el-table .warning-row {
  background: #f56c6c;
}

.el-table .submit-row {
  background: #e6a23c;
}

.el-table .success-row {
  background: #67c23a;
}
</style>
