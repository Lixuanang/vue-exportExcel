# vue-exportExcel

1.导入文件 **Export2Excel.js**

```javascript
import { export_json_to_excel } from './Export2Excel'
```

2.触发导出 Excel 的事件

```javascript
  // 导出excle
    async exportExcel() {
      // 请求数据
      const param = this.form
      const tableData = await exportExcel(param)
      if (tableData === 204) {
        this.$message({
          type: 'warning',
          message: '暂无数据可导出'
        })
        return
      }

      require.ensure([], () => {
        // excel 表格头
        const header = [
          '姓名',
          '手机号码',
          '注册时间',
          '用户类型',
          '最近上线时间'
        ]
        // 表头对应字段名
        const filterVal = [
          'username',
          'mobile',
          'create_at',
          'type',
          'online_at'
        ]

        const data = formatJson(filterVal, tableData) // 自行洗数据 按序排序的一个array数组
        const argumentsr = { header, data, filename: '客户信息表' } // 表名
        export_json_to_excel(argumentsr) // 导出
      })
    },
```
