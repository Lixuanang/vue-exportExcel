<template>
  <div class="wrap">
    <div class="meta">BD：{{userInfo.username}}（{{userInfo.contact_phone}}）<a style="color:#409EFF;margin-left:100px;" :href="`#/customerInfoManagement/customerDetail?id=${userInfo.fleet_id}&userid=${userInfo.userid}`">返回 &gt;</a>
    </div>
    <input type="file" @change="handleChange" accept=".xls,.xlsx">
    <a href="http://cdn.auv666.com/%E8%BD%A6%E8%BE%86%E5%88%97%E8%A1%A8.xls" style="color:#409EFF;" target="_blank" download="">示例文件下载</a>

    <el-table :data="carTableData" style="width:616px;" v-if="carTableData.length>0">
      <el-table-column type="index" width="50"></el-table-column>
      <el-table-column prop="number1" label="车牌信息" width="180"></el-table-column>
      <el-table-column label="荷载吨数">
        <template slot-scope="scope">
          {{scope.row.capacity/1000}}
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <i class="el-icon-loading" v-if="!scope.row.status"></i>
          <el-tag type="success" v-if="scope.row.status===1">成功</el-tag>
          <el-tag type="danger" v-if="scope.row.status===2">失败</el-tag>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<style lang="scss">
.wrap {
  background: #fff;
  padding: 20px;
  margin: 0 10px;
  min-height: 100%;
}
.meta {
  padding: 0 0 20px 0;
}
</style>
<script>
import XLSX from 'xlsx'
import { addCars } from '@/api/customer'

export default {
  data() {
    return {
      // 车辆列表数据
      carTableData: [],
      // 用户信息
      userInfo: {
        userid: '',
        username: '',
        fleet_id: '',
        contact_phone: ''
      },
      // 导入完成标示
      excel_tip: false
    }
  },
  methods: {
    async init() {
      this.userInfo.userid = this.$route.query.userid
      this.userInfo.username = this.$route.query.username
      this.userInfo.fleet_id = this.$route.query.fleet_id
      this.userInfo.contact_phone = this.$route.query.contact_phone
    },
    // 文件选择
    async handleChange(e) {
      const self = this

      if (self.excel_tip) return

      self.excel_tip = true
      let files = e.target.files

      let cars = []
      let data = null
      let workbook = null
      let fileReader = new FileReader()

      fileReader.onload = async function(ev) {
        try {
          data = ev.target.result
          workbook = XLSX.read(data, {
            type: 'binary'
          })
          cars = []
        } catch (e) {
          self.excel_tip = false
          console.log('文件类型不正确')
          return
        }

        // 遍历每张表读取
        for (let sheet in workbook.Sheets) {
          if (workbook.Sheets.hasOwnProperty(sheet)) {
            cars = cars.concat(XLSX.utils.sheet_to_json(workbook.Sheets[sheet]))
          }
        }

        cars.forEach(item => {
          self.carTableData.push({
            number1: item['车牌信息'],
            capacity: Math.round(Number(item['荷载吨位']) * 1000)
          })
        })

        // 批量添加车辆
        const res = await addCars({
          fleet_id: self.userInfo.fleet_id,
          cars: self.carTableData
        })
        // 成功标示添加 1 成功 2 失败
        self.carTableData.forEach((item, index) => {
          if (res.indexOf(index) > -1) {
            item.status = 2
          } else {
            item.status = 1
          }
        })
        // 失败排在前面
        self.carTableData = self.carTableData.sort(
          (a, b) => b.status - a.status
        )
        self.excel_tip = false
        // console.log(self.carTableData)
      }

      // 以二进制方式打开文件
      fileReader.readAsBinaryString(files[0])
    }
  },
  mounted() {
    this.init()
  }
}
</script>
