<template>
  <div class="home">
    <el-table :data="catalogueTableData.list" ref="refTable" height="500">
      <el-table-column
        label="日期"
        v-if="catalogueTableData.list.length !== 0"
        fixed="left"
      >
        <template slot-scope="scope">
          <template v-if="scope.row.date.error">
            <el-popover trigger="hover" placement="top">
              <p>{{ scope.row.date.tooltip }}</p>
              <div slot="reference" class="name-wrapper">
                <span class="error-content pd4">
                  {{ scope.row.date.content || '错误信息' }}
                </span>
              </div>
            </el-popover>
          </template>
          <template v-else>
            <div class="normal-content">
              {{ scope.row.date.content || '成功' }}
            </div>
          </template>
        </template>
      </el-table-column>
      <el-table-column
        v-for="(column, index) in catalogueTableData.headers"
        :key="index"
        :label="column"
        align="center"
        width="55"
      >
        <template slot-scope="scope">
          <template v-if="scope.row[column] && scope.row[column]['error']">
            <el-popover trigger="hover" placement="top">
              <p>{{ scope.row[column].tooltip }}</p>
              <div slot="reference" class="name-wrapper">
                <i class="error-color el-icon-close"></i>
              </div>
            </el-popover>
          </template>
          <template v-else-if="!scope.row[column]">
            <el-popover trigger="hover" placement="top">
              <p>缺失该日期，请检查该目录</p>
              <div slot="reference" class="name-wrapper">
                <i class="error-color el-icon-close"></i>
              </div>
            </el-popover>
          </template>
          <template v-else>
            <i class="el-icon-check"></i>
          </template>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  watch: {
    'catalogueTableData.list' (newVal) {
      const headerSet = new Set()
      console.log(newVal)
      newVal?.forEach(column =>
        Object.keys(column).map(item => item !== 'date' && headerSet.add(item))
      )
      this.catalogueTableData.headers = [...headerSet].sort((a, b) => a - b)
      console.log(this.catalogueTableData.headers)
    }
  },
  data () {
    return {
      catalogueTableData: {
        list: [],
        header: []
      }
    }
  },
  created () {
    this.$axios.get('/user/list').then(res => {
      this.catalogueTableData.list = [
        ...res.data.data.map(column => this.formatcataloguData(column))
      ]
      console.log(this.catalogueTableData.list)
    })
  },
  methods: {
    formatcataloguData (data) {
      const res = {}
      const errorArr = data['error']
      if (errorArr.length === 0 && !data['date_error']) {
        res['date'] = { content: data.date, error: false }
      } else if (data['date_error'] && errorArr.length === 0) {
        res['date'] = {
          content: data.date,
          error: true,
          tooltip: data.date_result
        }
      } else if (errorArr.length !== 0 && !data['date_error']) {
        res['date'] = {
          content: data.date,
          error: true,
          tooltip: '子级目录存在问题'
        }
      }
      errorArr.forEach(item => {
        res[item.hours] = {
          content: item.hours,
          error: true,
          tooltip: item.msg
        }
      })
      data['time_hours']?.forEach(item => {
        if (!res.hasOwnProperty(item)) {
          res[item] = { content: item, error: false }
        }
      })
      return res
    }
  }
}
</script>
<style lang="less" scoped>
.error-content {
  background: rgb(168, 79, 79);
  border-radius: 4px;
  text-align: center;
}
.error-color {
  color: red;
}
.pd4 {
  padding: 0 4px;
}
.hahahha {
  cursor: pointer;
}
::v-deep .name-wrapper.el-popover__reference {
  cursor: pointer;
}
::v-deep .el-table .el-table__body tr td {
  // background-color: #d4b021 !important;
  border: 2px solid transparent !important;
}
::v-deep .el-table .el-table__body tr td:hover {
  // background-color: #d4b021 !important;
  border: 2px solid red !important;
}
</style>
