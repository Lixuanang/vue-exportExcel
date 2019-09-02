# Vue 中导出 Excel 文件

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

> 参考文件`customerInfo.vue`、`Export2Excel.js`

# Vue 批量导入 Excel 文件中的数据

**Excel 中表头需固定,即表头需已知**

1.安装依赖并导入

```javascript
npm install xlsx

import XLSX from 'xlsx'
```

2.触发导入 Excel 文件的事件

```javascript
  handleChange(e) {
      const self = this;

      if (self.excel_tip) return;

      self.excel_tip = true;
      let files = e.target.files;

      let cars = [];
      let data = null;
      let workbook = null;
      let fileReader = new FileReader();

      fileReader.onload = async function(ev) {
        try {
          data = ev.target.result;
          workbook = XLSX.read(data, {
            type: "binary"
          });
          cars = [];
        } catch (e) {
          self.excel_tip = false;
          console.log("文件类型不正确");
          return;
        }

        // 遍历每张表读取
        for (let sheet in workbook.Sheets) {
          if (workbook.Sheets.hasOwnProperty(sheet)) {
            cars = cars.concat(
              XLSX.utils.sheet_to_json(workbook.Sheets[sheet])
            );
          }
        }

        cars.forEach(item => {
          self.carTableData.push({
            number1: item["车牌信息"],
            capacity: Math.round(Number(item["荷载吨位"]))
          });
        });

        // 请求接口 把Excel的数据传给后台

        // 批量添加车辆
        // const res = await addCars({
        //   fleet_id: self.userInfo.fleet_id,
        //   cars: self.carTableData
        // });
        // 成功标示添加 1 成功 2 失败
        // self.carTableData.forEach((item, index) => {
        //   if (res.indexOf(index) > -1) {
        //     item.status = 2;
        //   } else {
        //     item.status = 1;
        //   }
        // });

        // 失败排在前面
        // self.carTableData = self.carTableData.sort(
        //   (a, b) => b.status - a.status
        // );

        self.excel_tip = false;
      };

      // 以二进制方式打开文件
      fileReader.readAsBinaryString(files[0]);
    },
```

> 参考文件`carsImport.vue`
