<template>
  <div class="app-container">
    
    <!-- 搜索条件 -->
    <div class="filter-container">
      <el-input @keyup.enter.native="handleFilter" style="width: 200px;" class="filter-item" placeholder="学生姓名" v-model="listQuery.studentName">
      </el-input>

      <el-button class="filter-item" type="primary" v-waves icon="search" @click="handleFilter">搜索</el-button>
      
      <el-button class="filter-item" type="primary" @click="handleCreate"  icon="edit">添加</el-button>

      <el-button class="filter-item" type="primary" @click="handleDelAll"  icon="edit">批量删除</el-button>
     
      <el-button class="filter-item" type="primary" icon="document" @click="handleDownload">导出</el-button>
     
    </div>
   
    <!-- 表格 -->
    <el-table ref="multipleTable" @selection-change="handleSelectionChange" :data="list" v-loading.body="listLoading" element-loading-text="拼命加载中" border fit highlight-current-row  >
         <el-table-column type="selection" width="65">
         </el-table-column>

          <el-table-column align="center" label='序号' width="">
            <template scope="scope">
              {{scope.$index}}
            </template>
          </el-table-column>

          <el-table-column label="学生姓名" width="">
            <template scope="scope">
              {{scope.row.studentName}}
            </template>
          </el-table-column>

          <el-table-column label="性别" width="">
            <template scope="scope">
              <span v-if="scope.row.sex == 'man'">男</span>
              <span v-else>女</span>
            </template>
          </el-table-column>

          <el-table-column label="民族"  align="center" width="">
            <template scope="scope">
              <span v-if="scope.row.minzu == '10010' ">汉族</span>
              <span v-if="scope.row.minzu == '10011' ">壮族</span>
              <span v-if="scope.row.minzu == '10012' ">奶佬族</span>
            </template>
          </el-table-column>

          <el-table-column align="center" prop="created_at" label="二级学院" width="">
            <template scope="scope">
              <span v-if="scope.row.xueyuan == '40010' ">文学院</span>
              <span v-if="scope.row.xueyuan == '40011' ">外国语学院</span>
              <span v-if="scope.row.xueyuan == '40013' ">法学院</span>
            </template>
          </el-table-column>

            <el-table-column align="center"  label="入校时间" >
                <template scope="scope">
                  <!-- <i class="el-icon-time"></i> -->
                  <span>{{scope.row.ruxiaodate}}</span>
                </template>
            </el-table-column>

            <el-table-column align="center"  label="家庭住址" >
                <template scope="scope">
                  <!-- <i class="el-icon-time"></i> -->
                  <span>{{scope.row.address}}</span>
                </template>
            </el-table-column>

             <el-table-column align="center"  label="是否全日制" >
                <template scope="scope">
                  <!-- <i class="el-icon-time"></i> -->
                  <span v-if="scope.row.quanrizhi">是</span>
                  <span v-else>否</span>
                </template>
            </el-table-column>

            <el-table-column align="center"  label="操作" >
                <template scope="scope">
                   <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                   <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column>

    </el-table>
    <!-- 分页 -->
    <div v-show="!listLoading" class="pagination-container">
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page.sync="listQuery.currPage"
        :page-sizes="[10,20,30, 50]" :page-size="listQuery.pageSize" layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </div>
    <!-- 新增弹窗 -->
    <el-dialog title="表单新增" :visible.sync="dialogFormVisible">
          <el-form class="small-space" :model="temp" label-position="left" label-width="70px" style='width: 400px; margin-left:50px;'>

            <el-form-item label="字段1">
              <el-input v-model="temp.chnlId"></el-input>
            </el-form-item>

            <el-form-item label="字段2">
              <el-input v-model="temp.hisChnlId"></el-input>
            </el-form-item>

            <el-form-item label="字段3">
              <el-input v-model="temp.chnlName"></el-input>
            </el-form-item>

            <el-form-item label="字段4">
              <el-input v-model="temp.state"></el-input>
            </el-form-item>
           
          </el-form>

          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
           
            <el-button type="primary" @click="handleCreateSubmit">确 定</el-button>
          </div>
    </el-dialog>

  </div>
</template>

<script>
import { global } from '@/global/global';
import { api } from '@/global/api';

export default {
  data() {
    return {
        list: null,//表格list [].push({})
        total: null,
        listLoading: true,
        listQuery: {
            currPage: 1,
            pageSize: 10,
            studentName: '',
        },
        //新增弹窗表单字段
        temp: {
          "chnlId": "",
          "hisChnlId": "",
          "chnlName": "",
          "state": "",
          "isavailable": "",
          "orderNum": 10
        },
        typeOptions:[
          { key: '001', display_name: '类型1' },
          { key: '002', display_name: '类型2' },
          { key: '003', display_name: '类型3' }
        ],
        dialogFormVisible: false,
        multipleSelection: []
    }
  },
  mounted() {
    let vm = this;
    vm.getList();
  },
  methods: {
    initTemp(){
      let vm = this;
      vm.temp = {
          "chnlId": "",
          "hisChnlId": "",
          "chnlName": "",
          "state": "",
          "isavailable": "",
          "orderNum": 10
      }
    },
    //获取列表数据
    getList() {
        let vm = this;
        vm.listLoading = true;
        global.get( api.listStudents, { params: vm.listQuery },function(res){
                console.log('获取到数据-------：',JSON.stringify(res) )
                let data = res.body;
               if(data.resultCode == 0){ 
                    
                    vm.list = data.data.data;
                    console.log('列表数据：',vm.list);
                    vm.listQuery.currPage = data.data.currPage;
                    vm.listQuery.pageSize = data.data.pageSize;
                    vm.total = data.data.total;

                    vm.listLoading = false;
                    
               }else{
                    Message({
                        showClose: true,
                        message: res.body.resultMsg,
                        type: 'error'
                    });
                    vm.listLoading = false;
               }
        }, function(res){
            vm.listLoading = false;
        }, false)
    },
    //编辑
    handleEdit(index,row){
        let vm = this;
        console.log('编辑的row：',index,'-----',row);
        //带参跳转页面进行修改
        this.$router.push( { path: '/index/students/updateStudent', query: { id: row._id } } );
    },

    //单个删除
    handleDelete(index,row){
        let vm = this;
        //判断是否选中删除内容
        if(vm.multipleSelection.length === 0){
            this.$message({
              type: 'info',
              message: '请选择删除内容!'
            });
            return ;
        }
        console.log('单个删除选择的row：',index,'-----',row);
        //前端删除。
        // vm.list.splice(index,1)
        this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        }).then(() => {
            vm.delFun(row);
            this.$message({
              type: 'success',
              message: '删除成功!'
            });
        }).catch(() => {
            this.$message({
              type: 'info',
              message: '已取消删除'
            });          
        });
    },
    //调取接口的删除方法
    delFun(row){
      let vm = this;
      global.get( api.deleteStudent, { params: {'id': row._id} },function(res){
          vm.getList();  
      },function(res){
          alert('删除报错')
          vm.listLoading = false;
      }, false)
    },
    //批量删除
    handleDelAll(){
        let vm = this;
        if(vm.multipleSelection.length === 0){
            this.$message({
              type: 'info',
              message: '请选择删除内容!'
            });
            return ;
        }
        console.log('批量删除选择的row：',vm.multipleSelection)
        this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
            confirmButtonText: '确定批量删除',
            cancelButtonText: '取消',
            type: 'warning'
        }).then(() => {
            //前端依次执行单个删除操作
            for (var index in vm.multipleSelection) {
                console.log('======',vm.multipleSelection[index])
                vm.delFun(vm.multipleSelection[index]);
            };
            this.$message({
              type: 'success',
              message: '删除成功!'
            });
        }).catch(() => {
            this.$message({
              type: 'info',
              message: '已取消删除'
            });          
        });
    },
    //复选框
    handleSelectionChange(val) {
        this.multipleSelection = val;
    },
    //操作分页
    handleSizeChange(val) {
      this.listQuery.pageSize = val;
      this.getList();
    },
    //操作分页
    handleCurrentChange(val) {
        console.log('--------',val)
      this.listQuery.currPage = val;
      this.getList();
    },
    //搜索
    handleFilter() {
      this.getList();
    },
    //新增
    handleCreate() {
        //每次点击新增时 重置下新增表单数据，避免双向绑定的影响
        // this.initTemp();
        // this.dialogFormVisible = true;
        //跳到新增页面
        this.$router.push('/index/students/addStudent')
    },
    //新增提交
    handleCreateSubmit(){
        let vm = this;
        console.log('新增入参：',vm.temp)

        //这里作演示用，正式新增 请提交到接口
        vm.list.push(vm.temp)
        console.log('新增后',vm.list)
        
        this.dialogFormVisible = false;
    },
    //导出
    handleDownload() {
      let vm = this;
      require.ensure([], () => {
        const { export_json_to_excel } = require('../../vendor/Export2Excel');
        const tHeader = ['字段1', '字段2', '字段3', '字段4', '字段5'];
        const filterVal = ['chnlId', 'hisChnlId', 'chnlName', 'state', 'isavailable'];
        const list = vm.list;
        const data = vm.formatJson(filterVal, list);
        export_json_to_excel(tHeader, data, '导出的列表excel');
      })
    },
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => v[j]))
    }
  }
};
</script>
