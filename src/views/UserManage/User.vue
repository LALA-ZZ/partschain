<!--  -->
<template>
  <div class="userinfo">
    <el-card shadow="hover">
      <!-- 搜索与添加区域 -->
      <el-row :gutter="10">
        <el-col :span="5">
          <el-input v-model="queryInfo.name" placeholder="请搜索输入内容" clearable @clear="getUserList">
          </el-input>
        </el-col>
        <el-col :span="10">
          <el-button type="primary" icon="el-icon-search" @click="getUserList">搜索</el-button>
          <el-button type="primary" icon="el-icon-plus" @click="addDialogVisible = true">新增</el-button>
          <el-button type="danger" icon="el-icon-delete">批量删除</el-button>
        </el-col>

      </el-row>

      <!-- 下载区域 -->
      <book-type-option v-model="bookType"> </book-type-option>
      <el-button :loading="downloadLoading" type="success" icon="el-icon-document" @click="handleDownload">
        下载数据模板
      </el-button>
      <el-button size='mini' plain type='success' icon="el-icon-check" @click="confirmSave()">保存</el-button>

      <!-- 用户列表区域 -->
      <el-table :data="userList" border stripe highlight-current-row v-loading="loading" element-loading-text="拼命加载中"
        element-loading-spinner="el-icon-loading" @cell-dblclick='cellDblclick' :cell-class-name='getRowColumn'
        :header-cell-style="{background:'#eef1f6',color:'#606266'}" class="tb-edit">
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column type="index" label="序号" width="60px"></el-table-column>
        <el-table-column label="姓名" prop="name"></el-table-column>
        <el-table-column label="年龄" prop="age">
          <!-- 行内编辑 -->
          <template slot-scope="scope">
            <template v-if="scope.row.edit">
              <el-row>
                <el-col :span="10">
                  <el-input size="mini" v-model="scope.row.age" @blur="loseInputBlur"></el-input>
                </el-col>
                <!-- <el-col :span="10">
                  <el-button size="mini" type="warning" @click="cancelEdit(scope.row)">
                    取消
                  </el-button>
                </el-col> -->
              </el-row>
            </template>
            <span v-else>{{scope.row.age}}</span>
          </template>
        </el-table-column>
        <el-table-column label="生日" prop="birth"></el-table-column>
        <el-table-column label="性别" prop="sex"></el-table-column>
        <el-table-column label="地址" prop="addr" width="500px">

          <!-- 行内编辑 -->
          <template slot-scope="scope">
            <template v-if="scope.row.edit">
              <el-row>
                <el-col :span="10">
                  <el-input size="mini" v-model="scope.row.addr"></el-input>
                </el-col>
                <!-- <el-col :span=" 10">
                    <el-button size="mini" type="warning" @click="cancelEdit(scope.row)">
                      取消
                    </el-button>
                </el-col> -->
              </el-row>
            </template>
            <span v-else>{{scope.row.addr}}</span>
          </template>

        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <!-- {{scope.row}} 打印一行记录的数据 -->
            <el-switch v-model="value" active-color="#13ce66" inactive-color="#ff4949"
              @change="changeUserState(scope.row)">
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="300px">
          <template slot-scope="scope">
            <el-button v-show="scope.row.edit" type="info" size="mini" @click="cancelEdit(scope.row)">取消
            </el-button>
            <el-button v-show="scope.row.edit" type="success" size="mini" @click="confirmEdit(scope.row)">确认</el-button>
            <el-button v-show='!scope.row.edit' size="mini" @click="editInline(scope.row)">编辑</el-button>
            <!-- <el-button size="mini" @click="showEditDialog(scope.row)">编辑</el-button> -->
            <el-button size="mini" type="danger" @click="deleteUserById(scope.row)">删除</el-button>
            <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
              <el-button size="mini" type="warning" @click="setRole(scope.row)">设置</el-button>
            </el-tooltip>

          </template>

        </el-table-column>
      </el-table>

      <!-- 分页功能 -->
      <el-pagination background @size-change="handleSizeChange" @current-change="handleCurrentChange"
        :current-page="queryInfo.page" :page-sizes="[5, 8, 10, 20]" :page-size="queryInfo.limit"
        layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </el-card>

    <!-- 添加用户对话框 -->
    <!-- <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="30%" @closed="addDialogClose">
     
    <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="100px">
      <el-form-item label="用户名称" prop="name">
        <el-input v-model="addForm.name"></el-input>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
        <el-select v-model="addForm.sex" placeholder="请选择性别">
          <el-option label="男" value="shanghai"></el-option>
          <el-option label="女" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="年龄" prop="age">
        <el-input v-model="addForm.age"></el-input>
      </el-form-item>
      <el-form-item label="出生年月" prop="birth">
        <el-date-picker type="date" placeholder="选择日期" v-model="addForm.birth" style="width: 100%;"
          value-format="yyyy-MM-dd"></el-date-picker>
      </el-form-item>
      <el-form-item label="地址" prop="addr">
        <el-input v-model="addForm.addr"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="addDialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="addUser">确 定</el-button>
    </span>
    </el-dialog> -->

    <!-- 添加用户对话框 -->
    <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="70%" @closed="addDialogClose">
      <el-input v-model="addForm" placeholder="qingshuru " style="width=200px;"></el-input>
      <!-- 主体部分 -->
      <el-table :data="userList" border stripe highlight-current-row v-loading="loading" element-loading-text="拼命加载中"
        element-loading-spinner="el-icon-loading" @cell-dblclick='cellDblclick' :cell-class-name='getRowColumn'
        :header-cell-style="{background:'#eef1f6',color:'#606266'}">
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column type="index" label="序号" width="60px"></el-table-column>
        <el-table-column label="姓名" prop="name"></el-table-column>
        <el-table-column label="年龄" prop="age">
          <!-- 行内编辑 -->
          <template slot-scope="scope">
            <template v-if="scope.row.edit">
              <el-row>
                <el-col :span="10">
                  <el-input size="mini" v-model="scope.row.age" @blur="loseInputBlur"></el-input>
                </el-col>
                <!-- <el-col :span="10">
                  <el-button size="mini" type="warning" @click="cancelEdit(scope.row)">
                    取消
                  </el-button>
                </el-col> -->
              </el-row>
            </template>
            <span v-else>{{scope.row.age}}</span>
          </template>
        </el-table-column>
        <el-table-column label="生日" prop="birth"></el-table-column>
        <el-table-column label="性别" prop="sex"></el-table-column>
        <el-table-column label="地址" prop="addr" width="500px">
          <!-- 行内编辑 -->
          <template slot-scope="scope">
            <template v-if="scope.row.edit">
              <el-row>
                <el-col :span="10">
                  <el-input size="mini" v-model="scope.row.addr"></el-input>
                </el-col>
                <!-- <el-col :span=" 10">
                    <el-button size="mini" type="warning" @click="cancelEdit(scope.row)">
                      取消
                    </el-button>
                </el-col> -->
              </el-row>
            </template>
            <span v-else>{{scope.row.addr}}</span>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <!-- {{scope.row}} 打印一行记录的数据 -->
            <el-switch v-model="value" active-color="#13ce66" inactive-color="#ff4949"
              @change="changeUserState(scope.row)">
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="300px">
          <template slot-scope="scope">
            <el-button v-show="scope.row.edit" type="info" size="mini" @click="cancelEdit(scope.row)">取消
            </el-button>

            <el-button v-show="scope.row.edit" type="success" size="mini" @click="confirmEdit(scope.row)">确认</el-button>
            <el-button v-show='!scope.row.edit' size="mini" @click="editInline(scope.row)">编辑</el-button>
            <!-- <el-button size="mini" @click="showEditDialog(scope.row)">编辑</el-button> -->
            <el-button size="mini" type="danger" @click="deleteUserById(scope.row)">删除</el-button>
            <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
              <el-button size="mini" type="warning" @click="setRole(scope.row)">设置</el-button>
            </el-tooltip>

          </template>

        </el-table-column>
      </el-table>
      <!-- 分页功能 -->
      <el-pagination background @size-change="handleSizeChange" @current-change="handleCurrentChange"
        :current-page="queryInfo.page" :page-sizes="[5, 8, 10, 20]" :page-size="queryInfo.limit"
        layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
      <!-- 底部按钮部分 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 编辑用户的对话框 -->
    <el-dialog title="编辑用户" :visible.sync="editDialogVisible" width="30%">
      <el-form :model="editForm" :rules="editFormRules" ref="editFormRef" label-width="100px">
        <el-form-item label="姓名">
          <el-input v-model="editForm.name" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="出生年月">
          <el-input v-model="editForm.birth" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="地址" prop="addr">
          <el-input v-model="editForm.addr"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editUserById">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 分配角色对话框 -->
    <el-dialog title="分配角色" :visible.sync="setRoleDialogVisible" width="50%" @close="setRoleDialogClosed">
      <div>
        <p>当前的用户：{{userInfo.username}}</p>
        <p>当前的角色：{{userInfo.role_name}}</p>
        <p>分配新角色：
          <el-select v-model="selectedRoleId" placeholder="请选择">
            <el-option v-for="item in roleList" :key="item.id" :label="item.roleName" :value="item.id"></el-option>
          </el-select>
        </p>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRoleDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveRoleInfo">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>


<script>
import BookTypeOption from '../InStockManage/components/BookTypeOption.vue';
export default {
  data () {
    return {
      // 获取用户列表的参数
      queryInfo: {
        name: '',
        page: 1,//当前的页数
        limit: 10,//当前每页显示多少条记录
      },
      //存储用户的数据
      userList: [],
      //用户数据记录总数
      total: 0,
      loading: false,
      value: true,

      // 添加用户对话框的显示于隐藏
      addDialogVisible: false,

      // 增加用户表单数据
      addForm: {
        name: '',
        addr: '',
        birth: '',
        sex: '',
        age: ''
      },
      // 增加用户表单规则
      addFormRules: {
        name: [
          { required: true, message: '用户名称', trigger: 'blur' },
          { min: 1, max: 7, message: '长度在 1 到 7 个字符', trigger: 'blur' }
        ],
        sex: [
          { required: true, message: '请选择性别', trigger: 'change' }
        ],
        age: [
          { required: true, message: '请输入年龄', trigger: 'blur' }
        ],
        birth: [
          { required: true, message: '请输入出生年月', trigger: 'change' }
        ],
        addr: [
          { required: true, message: '请输入出生地址', trigger: 'blur' }
        ]
      },

      // 编辑用户对话框的显示与隐藏
      editDialogVisible: false,
      // 编辑对话框的数据表单
      editForm: {
        warename: '',
        wareaddres: '',
        warehead: '',
        warephone: '',
        warelevel: '',
        waredirec: ''
      },
      editFormRules: {
        addr: [
          { required: true, message: '请输入出生地址', trigger: 'blur' }
        ]
      },
      //控制分配角色对话框的显示
      setRoleDialogVisible: false,
      //需要被分配角色的用户信息
      userInfo: {},
      // 所有角色列表
      roleList: [],
      // 已选择的角色id值
      selectedRoleId: '',
      // 单元格横坐标
      tableRowIndex: null,
      // 单元格纵坐标
      tableColumnIndex: null,

      downloadLoading: false,
      filename: 'excel', //非必填
      // 导出的文件是否自动宽度
      autoWidth: true, //非必填
      // 导出的文本类型
      bookType: 'xlsx' //非必填
    };
  },
  created () {
    this.getUserList()
  },
  components: {
    BookTypeOption
  },

  computed: {},

  methods: {
    getUserList () {
      this.loading = true
      this.$http.get('/user/getUser',
        {
          params: this.queryInfo
        }
      ).then(res => {
        res = res.data
        console.log(res)
        if (res.code !== 20000) {
          this.$message.error(); ('获取用户数据失败！')
        }

        // 行内编辑
        this.userList = res.list.map(item => {//改写男女性别数据，并存放在userList数组中
          item.sex = item.sex === 0 ? '女' : '男';
          this.$set(item, 'edit', false)
          // this.$set(item, 'originalAddr', '')  // https://vuejs.org/v2/guide/reactivity.html
          item.originalAddr = item.addr //  will be used when user click the cancel botton
          item.originAge = item.age
          return item
        });


        this.total = res.count
        this.loading = false
      })

    },
    // 监听  页码大小 改变的事件,将新的页码大小更新到data中
    handleSizeChange (newSize) {
      console.log(newSize)
      this.queryInfo.limit = newSize //更新页码大小
      this.getUserList()
    },
    // 监听 当前页 变动时候触发的事件
    handleCurrentChange (newPage) {
      console.log(newPage)
      this.queryInfo.page = newPage //更新当前页码
      this.getUserList()
    },

    //监听swith开关状态的改变
    changeUserState (row) {
      console.log(row)
    },
    // 监听 添加用户 的对话框的关闭事件（清空对话框）
    addDialogClose () {
      this.$refs.addFormRef.resetFields()
    },

    // 添加用户
    addUser () {
      this.$refs.addFormRef.validate(validate => {
        if (!validate) return
        // 可以发起添加用户的网络请求
        this.$http.post('/user/add', this.addForm).then(res => {
          res = res.data
          console.log(res)
          if (res.code !== 20000) {
            this.$message.error('添加失败！')
          }
          this.$message.success(res.data.message)
          this.addDialogVisible = false
          this.getUserList()
        })
      })
    },


    // 显示编辑对话框
    showEditDialog (row) {
      this.editDialogVisible = true
      this.editForm = row


    },
    // 监听编辑对话框的关闭事件
    // editDialogClose () {
    //   this.$refs.editFormRef.resetFields()
    // },

    // 编辑用户
    editUserById () {
      this.$refs.editFormRef.validate(validate => {
        if (!validate) {
          return
        }
        this.$http.post('/user/edit', this.editForm).then(res => {
          console.log(res);
          if (res.data.code !== 20000) {
            return this.$message.error('编辑失败！')
          }
          // 关闭对话框
          this.editDialogVisible = false
          // 更新列表
          this.getUserList()
          // 提示更改成功
          this.$message.success('编辑成功！')
          console.log(this.editForm.addr)
        })

      })


    },

    // 删除用户
    deleteUserById (row) {
      console.log(row.id)
      this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.get('/user/del', {
          params: {
            id: row.id
          }
        })
      }).then(() => {
        this.$message({
          type: 'success',
          message: '删除成功!',

        });
        this.getUserList()
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    //展示分配角色的对话框
    setRole (userinfo) {
      this.userInfo = userinfo
      //分配用户角色前
      this.$http.get('roles').then(res => {
        if (res.meta.status !== 200) {
          return this.$message.error('获取角色列表失败！')
        }
        this.roleList = res.data
      })
      this.setRoleDialogVisible = true
    },
    //点击确定按钮分配角色
    saveRoleInfo () {
      if (!this.selectedRoleId) {
        return this.$message.error('请选择要分配的角色！')
      }
      this.$http.put(`users/${this.userInfo.id}/role`, { rid: this.selectedRoleId }).then(res => {
        if (res.meta.status !== 200) {
          return this.$message.error('更新角色失败！')
        }
        this.$message.success('更新角色成功！')
        this.getUserList()
        this.setRoleDialogVisible = false
      })
    },
    //监听分配角色对话框的关闭事件
    setRoleDialogClosed () {
      this.selectedRoleId = ''
      this.userinfo = {}
    },

    // 行内编辑，取消编辑
    editInline (row) {
      row.edit = !row.edit
    },
    // 取消编辑
    cancelEdit (row) {
      row.age = row.originAge
      row.addr = row.originalAddr
      row.edit = false
    },
    // 确认
    confirmEdit (row) {
      row.edit = false
      row.originalAddr = row.addr
      row.originAge = row.age

    },
    // // 对话框拖拽
    // handleDrag () {
    //   this.$refs.select.blur()
    // },
    // 表格双击编辑事件
    cellDblclick (row, column) {
      console.log(row);
      console.log(column);
      // console.log(cell);
      // console.log(event);
      console.log(row[column.property])
      // if (row[column.property]) {
      //   row[column.property].edit = true;
      //   setTimeout(() => {
      //     this.$refs[column.property].focus();
      //   }, 20);
      // }
      // 获取单元格横纵坐标
      this.tableRowIndex = row.index
      this.tableColumnIndex = column.index
    },
    // 数据中没有横纵坐标需要加上进一步判断
    getRowColumn ({ row, column, rowIndex, columnIndex }) {
      row.index = rowIndex
      column.index = columnIndex
    },
    // 失去焦点事件，清空坐标
    loseInputBlur () {
      this.tableColumnIndex = ''
      this.tableRowIndex = ''
    },

    // 下载部分
    handleDownload () {
      this.downloadLoading = true
      // 采用懒加载的方式
      import('@/vendor/Export2Excel').then(excel => {
        const tHeader = ['姓名', '年龄', '生日', '性别', '地址',]
        const filterVal = ['name', 'age', 'birth', 'sex', 'addr',]
        const list = this.userList
        const data = this.formatJson(filterVal, list)
        excel.export_json_to_excel({
          header: tHeader, //表头 必填
          data, //具体数据 必填
          filename: this.filename, //非必填
          autoWidth: this.autoWidth, //非必填
          bookType: this.bookType //非必填
        })
      })
      this.downloadLoading = false
    },
    formatJson (filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => v[j]))
    },


    // 单击保存，保存表格数据
    confirmSave () {
      console.log(JSON.stringify(this.userList))
    },




  }
}



</script>


<style lang="scss" scoped>
.userinfo {
  padding: 20px;
  padding-bottom: 20px;
}

.el-table {
  margin-top: 20px;
}

.el-pagination {
  margin-top: 20px;
}

// // 测试：行内可编辑
// .tb-edit .el-input {
//   display: none;
// }
// .tb-edit .current-row .el-input {
//   display: block;
// }
// .tb-edit .current-row .el-input + span {
//   display: none;
// }
</style>