<template>
  <div class="home">
    <h1>员工信息管理</h1>
    <el-form :inline="true">
      <el-form-item>
        <el-input v-model="queryForm.id" placeholder="員工编号" class="inputVal"></el-input>
        <el-input v-model="queryForm.dept" placeholder="所在部门" class="inputVal"></el-input>
        <el-input v-model="queryForm.name" placeholder="員工名字" class="inputVal"></el-input>
        <el-button @click="queryData" type="primary">查询</el-button>
      </el-form-item>

    </el-form>
    <el-row>
      <el-button @click="showDialogAdd" type="primary">添加</el-button>
      <el-button @click="delRows" type="danger">删除</el-button>
    </el-row>
    <el-table :data="tableData" style="margin-left: 200px; margin-right: 200px;" @selection-change="selectRow">
      <el-table-column type="selection"></el-table-column>
      <el-table-column label="員工编号">
        <template slot-scope="scope">
          {{ scope.row.id }}
        </template>
      </el-table-column>
      <el-table-column label="所在部门">
        <template slot-scope="scope">
          {{ scope.row.dept }}
        </template>
      </el-table-column>
      <el-table-column label="員工名字">
        <template slot-scope="scope">
          {{ scope.row.name }}
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="primary" class="inputVal" @click="showDialogEdit(scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination layout="total,sizes,prev,pager,next,jumper" :total="page.total" :page-sizes="[5, 10, 15]"
      :page-size="page.pageSize" :current-page="page.pageNum" @current-change="goPage"></el-pagination>


    <!-- 添加弹出框 -->
    <el-dialog :visible="visibleAdd">
      <el-form>
        <el-form-item>
          <el-input v-model="addForm.id" placeholder="員工编号"></el-input>
          <el-input v-model="addForm.dept" placeholder="所在部门"></el-input>
          <el-input v-model="addForm.name" placeholder="員工名字"></el-input>
          <el-button @click="addOrEdit('add')" type="primary">保存</el-button>
          <el-button @click="visibleAdd = false" type="cancel">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>

    <!-- 编辑弹出框 -->
    <el-dialog :visible="visibleEdit">
      <el-form :inline="false">
        <el-form-item>
          <el-input v-model="editForm.id" disabled placeholder="員工编号"></el-input>
          <el-input v-model="editForm.dept" placeholder="所在部门"></el-input>
          <el-input v-model="editForm.name" placeholder="員工名字"></el-input>
          <el-button @click="addOrEdit('edit')" type="primary">保存</el-button>
          <el-button @click="visibleEdit = false" type="cancel">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>

import Axios from 'axios'


const BASE_API = 'http://localhost:9999/dept'


export default {
  name: 'DeptView',
  data() {
    return {
      queryForm: { "id": "", "name": "", "dept": "" },
      addForm: { "id": "", "name": "", "dept": "","operate":"add" },
      editForm: { "id": "", "name": "", "dept": "","operate":"edit" },
      page: { "total": 20, "pageSize": 5, "pageNum": 1 },
      delIds: "",
      visibleAdd: false,
      visibleEdit: false,
      tableData: [
        { "id": "11", "name": "工程部", "dept": "王二狗" },
        { "id": "12", "name": "工程部", "dept": "王大狗" },
        { "id": "13", "name": "销售部", "dept": "王三狗" },
        { "id": "14", "name": "销售部", "dept": "王四狗" },
        { "id": "15", "name": "技术部", "dept": "王五狗" },
        { "id": "16", "name": "技术部", "dept": "王六狗" }
      ]
    }
  },
  methods: {
    queryData() {
      console.log('queryData---start');
      Axios.post(BASE_API + '/list', {pageNum:this.page.pageNum,pageSize: this.page.pageSize, body: this.queryForm })
        .then(resp => {
          const data = resp.data;
          this.tableData = data.list;
          this.page.total = data.total;
          this.page.pageNum = data.pageNum;
        }).catch(error => {
          console.log('打印错误信息:' + error)
        });
    },
    showDialogAdd() {
      console.log('showDialogAdd---start');
      this.editForm.id = '';
      this.editForm.dept = '';
      this.editForm.name = '';

      this.visibleAdd = true;
    },
    showDialogEdit(row) {
      console.log('showDialogEdit---start');
      this.editForm.id = row.id;
      this.editForm.dept = row.dept;
      this.editForm.name = row.name;

      this.visibleEdit = true;
    },
    addOrEdit(operate){
      let postData = {};
      if('add' == operate){
        postData = this.addForm;
      }else{
        postData = this.editForm;
      }
      Axios.post(BASE_API+'/save',postData)
          .then(resp=>{
            if(resp.data>0){
              this.$message('保存成功');
              this.queryData();
            }else{
              this.$message('保存失敗');
            }
          })
    },
    selectRow(rows) {
      let ids = '';
      rows.forEach(element => {
        ids += element.id + ',';
      });
      this.delIds = ids.substring(0, ids.lastIndexOf(','));
      console.log('delIds is:' + this.delIds)


    },
    delRows() {
        console.log('del ids:'+this.delIds)
        Axios.delete(BASE_API+'/removes',{params: this.delIds})
        .then(resp=>{
          if(this.resp.data>0){
            this.$message('刪除成功');
            this.queryData();
          }else{
            this.$message('刪除失敗');
          }
        }).catch(err=>{
            console.log('打印錯誤信息:'+ err);
            this.$message('刪除失敗');
        });
    },
    goPage(iPage) {
      console.log('go page:' + iPage)
      this.page.pageNum = iPage;
      this.queryData();
    }

  },
  mounted: function () {
    this.queryData();
  }
}
</script>

<style>
.inputVal {
  width: 160px;
}
</style>
