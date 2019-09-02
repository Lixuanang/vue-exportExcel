<!-- 客户信息List -->
<template>
  <div class="wrap">
    <div class="wrapper">
      <el-card>
        <el-form label-width="80px" label-position="left" v-loading="loading.dataDic">
          <el-row :gutter="20">
            <el-col :span="12">
              <el-row>
                <el-form-item label="用户类型">
                  <el-radio
                    v-model="form.type.checked"
                    v-for="(c, index) in form.type.options"
                    :label="c.value"
                    :key="index"
                  >{{ c.name }}</el-radio>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="线路区域">
                  <el-row>
                    <el-col :span="6.5">
                      <el-input v-model="form.region.load" placeholder="请输入线路区域" @keyup.enter.native='filterSearch'></el-input>
                    </el-col>
                    <el-col :span="1" class="split">-</el-col>
                    <el-col :span="6.5">
                      <el-input v-model="form.region.unload" placeholder="请输入线路区域"  @keyup.enter.native='filterSearch'></el-input>
                    </el-col>
                  </el-row>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="货品介质">
                  <el-checkbox-group v-model="form.medium.checked">
                    <el-checkbox
                      v-for="(m, index) in form.medium.options"
                      :label="m.value"
                      :key="index"
                      class="medium-checkbox"
                    >{{ m.name }}</el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="车队类型">
                  <el-radio
                    v-model="form.fleet_type.checked"
                    v-for="(f, index) in form.fleet_type.options"
                    :label="f.value"
                    :key="index"
                  >{{ f.name }}</el-radio>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="沟通态度">
                  <el-checkbox-group v-model="form.communicate.checked">
                    <el-checkbox
                      v-for="(c, index) in form.communicate.options"
                      :label="c.value"
                      :key="index"
                    >{{ c.name }}</el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
              </el-row>
            </el-col>
            <el-col :span="12">
              <el-row>
                <el-form-item label="注册时间">
                  <el-row class="register-min-width">
                    <el-col :span="14">
                      <el-date-picker
                        class="date-picker"
                        v-model="form.registerTime.time"
                        type="daterange"
                        range-separator="至"
                        start-placeholder="开始日期"
                        end-placeholder="结束日期"
                        @change="registerTimeChange"
                      ></el-date-picker>
                    </el-col>
                    <el-col :span="8" class="label-wrapper">
                      <el-radio-group
                        v-model="form.registerTime.flag"
                        @change="checkRecentlyRegisterTime"
                      >
                        <el-radio-button
                          v-for="r in recently"
                          :label="r.key"
                          :key="r.key"
                        >{{r.label}}</el-radio-button>
                      </el-radio-group>
                    </el-col>
                  </el-row>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="上线时间">
                  <el-row class="online-min-width">
                    <el-col :span="14">
                      <el-date-picker
                        v-model="form.onlineTime.time"
                        type="daterange"
                        range-separator="至"
                        start-placeholder="开始日期"
                        end-placeholder="结束日期"
                        @change="onlineTimeChange"
                      ></el-date-picker>
                    </el-col>
                    <el-col :span="8" class="label-wrapper">
                      <el-radio-group
                        v-model="form.onlineTime.flag"
                        @change="checkRecentlyOnlineTime"
                      >
                        <el-radio-button
                          v-for="r in recently"
                          :label="r.key"
                          :key="r.key"
                        >{{r.label}}</el-radio-button>
                      </el-radio-group>
                    </el-col>
                  </el-row>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="业务类型">
                  <el-radio
                    v-model="form.transport.checked"
                    v-for="(t, index) in form.transport.options"
                    :label="t.value"
                    :key="index"
                  >{{ t.name }}</el-radio>
                </el-form-item>
              </el-row>

              <el-row>
                <el-form-item label="性格沟通">
                  <el-checkbox-group v-model="form.characters.checked">
                    <el-checkbox
                      v-for="(c, index) in form.characters.options"
                      :label="c.value"
                      :key="index"
                    >{{ c.name }}</el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
              </el-row>
              <el-row>
                <el-form-item label="心理标签">
                  <el-checkbox-group v-model="form.psychology.checked">
                    <el-checkbox
                      v-for="(p, index) in form.psychology.options"
                      :label="p.value"
                      :key="index"
                    >{{ p.name }}</el-checkbox>
                  </el-checkbox-group>
                </el-form-item>
              </el-row>
            </el-col>
          </el-row>
        </el-form>
      </el-card>
      <el-card v-loading="loading.table">
        <el-row class="search" :gutter="20">
          <el-col :span="12" style="opacity:0">.</el-col>
          <el-col :span="12">
            <el-row :gutter="20" type="flex" justify="space-between">
              <el-col :span="16">
                <el-input
                  v-model="form.mobile"
                  maxlength="11"
                  placeholder="请输入客户手机号码"
                  suffix-icon="el-icon-search"
                  class="search-input"
                  @input="form.mobile=form.mobile.replace(/\D/g,'')"
                  @keyup.enter.native='filterSearch'
                />
              </el-col>
              <el-col :span="5">
                <el-button
                  type="primary"
                  @click="clearSearch"
                  :loading="loading.clear"
                  class="btn-search"
                >清 空</el-button>
              </el-col>
              <el-col :span="5">
                <el-button
                  type="primary"
                  @click="filterSearch"
                  :loading="loading.filter"
                  class="btn-search"
                >筛 选</el-button>
              </el-col>
            </el-row>
          </el-col>
        </el-row>
        <el-row class="table" justify="center">
          <el-tooltip class="tooltip" effect="dark" content="点击导出Excel" placement="bottom">
            <i class="el-icon-download" @click="exportExcel()"></i>
          </el-tooltip>

          <el-col>
            <el-table :data="tableData" style="width: 100%">
              <el-table-column
                header-align="center"
                align="center"
                v-for="(t, index) in table"
                :label="t.label"
                :key="index"
                :prop="t.prop"
                :width="t.width"
              />
              <el-table-column label="操作" width="200" header-align="center" align="center">
                <template slot-scope="scope">
                  <el-button
                    type="text"
                    size="small"
                    @click="handleClick(scope.row.id, scope.row.user_id)"
                  >查看</el-button>
                </template>
              </el-table-column>
            </el-table>
            <el-row type="flex" justify="center">
              <el-pagination
                v-show="tableData.length != 0"
                :total="form.pagination.total"
                :current-page="form.pagination.pageNum"
                :page-sizes="form.pagination.pageSizes"
                :page-size="form.pagination.pageSize"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :layout="form.pagination.layout"
              ></el-pagination>
            </el-row>
          </el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
import { getCustomerList, exportExcel } from '@/api/customer'
import { export_json_to_excel } from '@/utils/Export2Excel'
import dataDic from '@/utils/datadictionary'
import { addByTransDate, parseTime, formatJson } from '@/utils/index'
import {
  custTypeOptions,
  mediumOptions,
  fleetTypeOptions,
  transportOptions
} from '@/utils/datadictionary_options'
export default {
  data() {
    return {
      form: {
        // 用户类型
        type: { checked: null, options: custTypeOptions },
        // 线路区域
        region: { load: '', unload: '' },
        // 商品介质
        medium: { checked: [], options: mediumOptions },
        // 车队类型
        fleet_type: { checked: -1, options: fleetTypeOptions },
        // 沟通态度
        communicate: { checked: [], options: [] },
        // 注册时间
        registerTime: { time: ['', ''], flag: -1 },
        // 上线时间
        onlineTime: { time: ['', ''], flag: -1 },
        // 业务类型
        transport: {
          isIndeterminate: false,
          checkAll: false,
          checked: -1,
          options: transportOptions
        },
        // 性格沟通
        characters: { checked: [], options: [] },
        // 心理标签
        psychology: { checked: [], options: [] },
        // 筛选 手机号
        mobile: '',
        // 分页
        pagination: {
          total: 0,
          pageNum: 1,
          // pageSizes: [2, 4, 6, 8, 10],
          // pageSize: 2,
          pageSizes: [10, 20, 30, 40, 50],
          pageSize: 10,
          layout: 'total, sizes, prev, pager, next, jumper'
        }
      },
      recently: [
        { label: '近一周', key: 1 },
        { label: '近30天', key: 2 },
        { label: '近半年', key: 3 }
      ],
      // 表头
      table: [
        { prop: 'username', label: '姓名', width: '' },
        { prop: 'mobile', label: '手机', width: '' },
        { prop: 'create_at', label: '注册时间', width: '' },
        { prop: 'type', label: '用户类型', width: '' },
        { prop: 'login_at', label: '最近上线时间', width: '' }
      ],
      // 表格数据
      tableData: [],
      // 加载 清空按钮 过滤按钮
      loading: { clear: false, filter: false, dataDic: true, table: true }
    }
  },
  methods: {
    // 注册时间
    registerTimeChange(val) {
      if (val === null) {
        // 取消近一周 / 近一月 / 近半年的标志
        this.form.registerTime.flag = -1
      }
    },
    // 上线时间
    onlineTimeChange(val) {
      if (val === null) {
        // 取消近一周 / 近一月 / 近半年的标志
        this.form.onlineTime.flag = -1
      }
    },
    // 筛选
    async filterSearch() {
      const self = this
      //loading时间
      const duration = 1500
      // 手机号码
      const mobile = this.form.mobile
      // 校验线路区域是否存在单填
      const checkRegion = self.checkRegion()
      // 线路区域未填全
      if (checkRegion === -1) return
      // 手机号位数校验
      if (mobile !== '') {
        // 校验手机号是否为11位
        const checkMobile = self.checkMobile(mobile)
        if (!checkMobile) return
      }
      // 重置当前页和每页显示条数
      self.form.pagination.pageNum = 1
      self.form.pagination.pageSize = 10
      await self.getcustomerInfo()
      self.loading.filter = true
      self.$message({
        type: 'success',
        message: '已筛选出数据',
        duration
      })
      setTimeout(() => {
        self.loading.filter = false
      }, duration)
    },
    // 清空
    async clearSearch() {
      const self = this
      const obj = self.form
      const duration = 1500
      // 清空按钮开始loading
      self.loading.clear = true
      // 清空条件
      obj.mobile = ''
      obj.type.checked = null
      obj.region.load = ''
      obj.region.unload = ''
      obj.medium.checked = []
      obj.fleet_type.checked = -1
      obj.communicate.checked = []
      obj.registerTime.time = ['', '']
      obj.registerTime.flag = -1
      obj.onlineTime.time = ['', '']
      obj.onlineTime.flag = -1
      obj.transport.checked = -1
      obj.characters.checked = []
      obj.psychology.checked = []
      self.$message({
        type: 'success',
        message: '已清空所有筛选条件',
        duration
      })
      // 刷新表格
      await self.getcustomerInfo()
      // 清空按钮结束loading
      setTimeout(() => {
        self.loading.clear = false
      }, duration)
    },
    // 点击查看
    handleClick(id, userid) {
      // 跳转详情页
      this.$router.push({
        path: `/customerInfoManagement/customerDetail?id=${id}&userid=${userid}`
      })
    },
    // 分页
    handleSizeChange(val) {
      // 改变当前页和页面显示数据条数
      this.form.pagination.pageNum = 1
      this.form.pagination.pageSize = val
      // 重新获取表格数据
      this.getcustomerInfo()
    },
    handleCurrentChange(val) {
      // 改变页面显示数据条数
      this.form.pagination.pageNum = val
      // 重新获取表格数据
      this.getcustomerInfo()
    },
    // 获取表格信息
    async getcustomerInfo() {
      const params = this.form
      const customerList = await getCustomerList(params)
      // 无数据 状态码204
      if (customerList[0]) {
        if (customerList[0].code === 204) {
          this.tableData = []
          // 表格loading结束
          this.loading.table = false
        }
        return
      }
      // 赋值表格数据
      this.tableData = customerList.data.result
      // 赋值总页数
      this.form.pagination.total = customerList.data.total
      // 表格loading结束
      this.loading.table = false
    },
    // 注册时间
    checkRecentlyRegisterTime() {
      const flag = this.form.registerTime.flag
      const obj = this.form.registerTime
      this.recentlyCheckCommonFn(obj, flag)
    },
    // 上线时间
    checkRecentlyOnlineTime() {
      const flag = this.form.onlineTime.flag
      const obj = this.form.onlineTime
      this.recentlyCheckCommonFn(obj, flag)
    },
    /**
     * 计算近一周、一月、半年日期
     * @param
     *
     * @ele 注册时间/上线时间对象 包含 start/end/flag
     * @flag 近一周为1/近30天为2/近半年为3
     */
    recentlyCheckCommonFn(ele, flag) {
      const today = new Date()
      // 开始时间
      let start = ''
      // 结束时间
      let end = parseTime(today, '{y}-{m}-{d}')
      // 距今天差的天数
      let limit = 0
      switch (flag) {
        case 1:
          limit = 7 // 据今天前一周的日期
          break
        case 2:
          limit = 30 // 据今天前一月的日期
          break
        case 3:
          limit = 183 // 据今天前半年的日期
          break
      }
      start = addByTransDate(today, limit)
      // ele.start = start
      ele.time = [start, end]
    },
    // 校验线路区域 起/止线路未选提示
    checkRegion() {
      const self = this
      const regionLoad = self.form.region.load
      const regionUnload = self.form.region.unload
      // 线路区域起点/终点一个为空时提示
      if (regionLoad === '' && regionUnload !== '') {
        self.$message({
          message: '请填写完整的线路区域',
          type: 'warning'
        })
        // 调用校验线路区域函数 返回值为-1时,不调筛选接口
        return -1
      } else if (regionLoad !== '' && regionUnload === '') {
        self.$message({
          message: '请填写完整的线路区域',
          type: 'warning'
        })
        // 调用校验线路区域函数 返回值为-1时,不调筛选接口
        return -1
      }
    },
    // 校验筛选手机号码(是否为11位)
    checkMobile(mobile) {
      const duration = 1500
      if (mobile.length === 11) {
        return true
      }
      this.$message({
        type: 'warning',
        message: '请输入11位手机号码',
        duration
      })
      return false
    },
    // 导出excle
    async exportExcel() {
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
        // 字段
        const filterVal = [
          'username',
          'mobile',
          'create_at',
          'type',
          'online_at'
        ]
        const data = formatJson(filterVal, tableData) // 自行洗数据 按序排序的一个array数组
        const argumentsr = { header, data, filename: '客户信息表' }
        export_json_to_excel(argumentsr)
      })
    },
    // 获取数据字典
    async getDataDic() {
      const self = this
      // 获取沟通态度选项
      const communicate = dataDic.getCommunicate().then(res => {
        self.form.communicate.options = res
      })
      // 获取性格沟通选项
      const character = dataDic.getCharacter().then(res => {
        self.form.characters.options = res
      })
      // 获取心理标签
      const psychology = dataDic.getPsychology().then(res => {
        self.form.psychology.options = res
      })
      // 数据字典结束loading
      Promise.all([communicate, character, psychology])
        .then(() => {
          self.loading.dataDic = false
        })
        .catch(error => {
          console.log(error) // 失败了，打出 '失败'
        })
        .finally(() => {
          self.loading.dataDic = false
        })
    },
    // 初始化
    init() {
      const self = this
      // 获取客服表格信息
      self.getcustomerInfo()
      // 获取数据字典信息
      self.getDataDic()
    }
  },
  mounted() {
    this.init()
  }
}
</script>

<style lang='scss'>
.el-input {
  min-width: 100px;
}
.wrap {
  .wrapper {
    padding: 0 10px;
    .el-date-editor .el-range-separator {
      width: 7%;
    }
    .el-card {
      margin-bottom: 10px;
      &:last-child {
        margin-bottom: 0;
      }
    }
    .el-checkbox {
      margin-left: 0;
      margin-right: 30px;
      &:last-child {
        margin-right: 0;
      }
    }
    .online-min-width,
    .register-min-width {
      min-width: 460px;
    }
    .split,
    .time {
      color: #606266;
      user-select: none;
      cursor: pointer;
      text-align: center;
    }
    .label-wrapper {
      min-width: 252px;
      display: flex;
      justify-content: space-around;
      .time {
        font-size: 15px;
      }
    }
    .search {
      margin-bottom: 22px;
      .btn-search {
        width: 100%;
      }
    }
    .table {
      position: relative;
      .tooltip {
        position: absolute;
        top: 10px;
        right: 10px;
        font-size: 25px;
        color: #909399;
        z-index: 2;
        cursor: pointer;
      }
      .el-pagination {
        margin-top: 20px;
      }
    }
    date-picker.el-range-separator {
      width: 6%;
    }
    // @media screen and (max-width: 1760px) {
    //   .label-wrapper {
    //     margin-top: 10px;
    //   }
    // }
  }
}
</style>
