<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">内网IP</label>
        <el-input v-model="query.hostIp" clearable placeholder="内网IP" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">壁垒机IP</label>
        <el-input v-model="query.jumpIp" clearable placeholder="壁垒机IP" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">钱包码</label>
        <el-input v-model="query.ownerId" clearable placeholder="钱包码" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">主机码</label>
        <el-input v-model="query.hotspotId" clearable placeholder="主机码" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">主机品牌ID</label>
        <el-input v-model="query.brandId" clearable placeholder="主机品牌ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">用户ID</label>
        <el-input v-model="query.userId" clearable placeholder="用户ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">磁盘使用率</label>
        <el-input v-model="query.disk" clearable placeholder="磁盘使用率" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="内网IP" prop="hostIp">
            <el-input v-model="form.hostIp" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="壁垒机IP">
            <el-input v-model="form.jumpIp" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="账号" prop="account">
            <el-input v-model="form.account" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input v-model="form.password" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="钱包码" prop="ownerId">
            <el-input v-model="form.ownerId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="主机码" prop="hotspotId">
            <el-input v-model="form.hotspotId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="主机品牌ID" prop="brandId">
            <el-select v-model="form.brandId" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.online_brand"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="用户ID" prop="userId">
            未设置字典，请手动设置 Select
          </el-form-item>
          <el-form-item label="创建日期" prop="createTime">
            <el-date-picker v-model="form.createTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="更新时间">
            <el-date-picker v-model="form.updateTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="磁盘使用率">
            <el-input v-model="form.disk" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="主机名称">
            <el-input v-model="form.hntName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="主机状态ID" prop="onlineStatus">
            <el-select v-model="form.onlineStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.online_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="hostIp" label="内网IP" />
        <el-table-column prop="jumpIp" label="壁垒机IP" />
        <el-table-column prop="account" label="账号" />
        <el-table-column prop="password" label="密码" />
        <el-table-column prop="ownerId" label="钱包码" />
        <el-table-column prop="hotspotId" label="主机码" />
        <el-table-column prop="brandId" label="主机品牌ID">
          <template slot-scope="scope">
            {{ dict.label.online_brand[scope.row.brandId] }}
          </template>
        </el-table-column>
        <el-table-column prop="disk" label="磁盘使用率" />
        <el-table-column prop="hntName" label="主机名称" />
        <el-table-column prop="minerHeight" label="主机区块高度" />
        <el-table-column prop="liveHeight" label="主网区块高度" />
        <el-table-column prop="createTime" label="创建日期" />
        <el-table-column prop="updateTime" label="更新时间" />
        <el-table-column prop="onlineStatus" label="主机状态ID">
          <template slot-scope="scope">
            {{ dict.label.online_status[scope.row.onlineStatus] }}
          </template>
        </el-table-column>
        <el-table-column v-if="checkPer(['admin','sysHostInfo:edit','sysHostInfo:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudSysHostInfo from '@/api/sysHostInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, hostIp: null, jumpIp: null, account: null, password: null, ownerId: null, hotspotId: null, brandId: null, userId: null, createTime: null, updateTime: null, disk: null, hntName: null, minerHeight: null, liveHeight: null, onlineStatus: null }
export default {
  name: 'SysHostInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['online_brand', 'online_status'],
  cruds() {
    return CRUD({ title: '主机接口', url: 'api/sysHostInfo', idField: 'id', sort: 'id,desc', crudMethod: { ...crudSysHostInfo }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'sysHostInfo:add'],
        edit: ['admin', 'sysHostInfo:edit'],
        del: ['admin', 'sysHostInfo:del']
      },
      rules: {
        hostIp: [
          { required: true, message: '内网IP不能为空', trigger: 'blur' }
        ],
        account: [
          { required: true, message: '账号不能为空', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '密码不能为空', trigger: 'blur' }
        ],
        ownerId: [
          { required: true, message: '钱包码不能为空', trigger: 'blur' }
        ],
        hotspotId: [
          { required: true, message: '主机码不能为空', trigger: 'blur' }
        ],
        brandId: [
          { required: true, message: '主机品牌ID不能为空', trigger: 'blur' }
        ],
        userId: [
          { required: true, message: '用户ID不能为空', trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: '创建日期不能为空', trigger: 'blur' }
        ],
        onlineStatus: [
          { required: true, message: '主机状态ID不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'hostIp', display_name: '内网IP' },
        { key: 'jumpIp', display_name: '壁垒机IP' },
        { key: 'ownerId', display_name: '钱包码' },
        { key: 'hotspotId', display_name: '主机码' },
        { key: 'brandId', display_name: '主机品牌ID' },
        { key: 'userId', display_name: '用户ID' },
        { key: 'disk', display_name: '磁盘使用率' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
