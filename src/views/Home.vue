<template>
  <div class="about">
    <el-table
      :data="tableData.list"
      style="width: 100%"
      :height="500"
    >
      <el-table-column
        v-for="(column, index) in tableData.headers"
        :key="index"
        :fixed="column === '序号' ? 'left' : false"
        :label="column"
        width="120"
      >
        <template slot-scope="scope">
          <template v-if="scope.row[column].error">
            <el-popover trigger="hover" placement="top">
              <p>{{ scope.row[column].tooltip }}</p>
              <div slot="reference" class="name-wrapper">
                <span class="error-content pd4">
                  {{ scope.row[column].content || "错误信息" }}
                </span>
              </div>
            </el-popover>
          </template>
          <template v-else>
            <el-popover
              trigger="hover"
              placement="top"
              v-if="scope.row[column].tooltip"
            >
              <p>{{ scope.row[column].tooltip }}</p>
              <div slot="reference" class="name-wrapper">
                <div class="normal-content">
                  {{ scope.row[column].content || "成功" }}
                </div>
              </div>
            </el-popover>
            <div v-else class="normal-content">
              {{ scope.row[column].content || "成功" }}
            </div>
          </template>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
  <script>
export default {
  watch: {
    'tableData.list' (newVal) {
      const headerSet = new Set()
      newVal?.forEach(column => Object.keys(column).map(item => (item !== 'file') && headerSet.add(item)))
      this.tableData.headers = [...headerSet]
      // console.log(this.tableData.headers);
    },
  },
  data () {
    return {
      tableData: {
        list: [],
        header: []
      },
    }
  },
  created () {
    this.$axios.get('/user/tableData').then((res) => {
      this.tableData.list = [...res.data.data.map((column,index) => this.formatDataHeader(column,index))]
      console.log(this.tableData.list);
    })
  },
  methods: {
    formatDataHeader (data, index) {
      const res = {};
      const errorObject = data["error"];
      if (Object.keys(data["error"]).length === 0) {
        res['序号'] = { content: index + 1, error: false, tooltip: data.file }
      } else {
        res['序号'] = { content: index + 1, error: true, tooltip: data.file }
      }
      for (const key in errorObject) {
        res[key] = { content: '', error: true, tooltip: errorObject[key] }
      }
      for (const key in data) {
        if (key === "error") continue;  // if key equal 'error', jump
        const isError = Reflect.has(res, key)
        if (isError) {
          res[key]['content'] = data[key]
        } else {
          res[key] = { content: data[key], error: false }
        }
      }
      return res;
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
::v-deep .name-wrapper.el-popover__reference {
  cursor: pointer;
}
</style>