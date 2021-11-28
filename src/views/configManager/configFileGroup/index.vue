<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="配置文件组主键">
            <el-input v-model="form.pkConfigFileGroup" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="配置文件组名称" prop="name">
            <el-input v-model="form.name" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="创建者">
            <el-radio v-for="item in dict.user_status" :key="item.id" v-model="form.createBy" :label="item.value">{{ item.label }}</el-radio>
          </el-form-item>
          <el-form-item label="更新者">
            <el-radio v-for="item in dict.user_status" :key="item.id" v-model="form.updateBy" :label="item.value">{{ item.label }}</el-radio>
          </el-form-item>
          <el-form-item label="创建日期">
            <el-date-picker v-model="form.createTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="更新时间">
            <el-date-picker v-model="form.updateTime" type="datetime" style="width: 370px;" />
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
        <el-table-column prop="pkConfigFileGroup" label="配置文件组主键" />
        <el-table-column prop="name" label="配置文件组名称" />
        <el-table-column prop="createBy" label="创建者">
          <template slot-scope="scope">
            {{ dict.label.user_status[scope.row.createBy] }}
          </template>
        </el-table-column>
        <el-table-column prop="updateBy" label="更新者">
          <template slot-scope="scope">
            {{ dict.label.user_status[scope.row.updateBy] }}
          </template>
        </el-table-column>
        <el-table-column prop="createTime" label="创建日期" />
        <el-table-column prop="updateTime" label="更新时间" />
        <el-table-column v-if="checkPer(['admin','configFileGroup:edit','configFileGroup:del'])" label="操作" width="150px" align="center">
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
import crudConfigFileGroup from '@/api/configFileGroup'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { pkConfigFileGroup: null, name: null, createBy: null, updateBy: null, createTime: null, updateTime: null }
export default {
  name: 'ConfigFileGroup',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['user_status', 'user_status'],
  cruds() {
    return CRUD({ title: 'ConfigFileGroupController', url: 'api/configFileGroup', idField: 'pkConfigFileGroup', sort: 'pkConfigFileGroup,desc', crudMethod: { ...crudConfigFileGroup }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'configFileGroup:add'],
        edit: ['admin', 'configFileGroup:edit'],
        del: ['admin', 'configFileGroup:del']
      },
      rules: {
        name: [
          { required: true, message: '配置文件组名称不能为空', trigger: 'blur' }
        ]
      }}
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
