<template>
  <!--  住房管理-->
  <div style="padding: 10px">
    <!--    功能区-->
    <div style="margin:10px">
      <div style="width: 100%; text-align:center;padding-left: 30px; font-weight: bolder; color: dodgerblue" >文物信息</div>
    </div>
    <!--    搜索区-->
    <div style="margin: 10px" >
      <el-input v-model="search1" placeholder="请输入'文物名称'" style="width:20%" clearable />
      <el-button type="warning" @click="load">按'文物名称'查询</el-button>
      <el-input v-model="search2" placeholder="请输入'博物馆'" style="width:20%" clearable />
      <el-button type="primary" @click="load2">按'博物馆'查询</el-button>
      <el-input v-model="search3" placeholder="请输入'文物年份'" style="width:20%" clearable />
      <el-button type="success" @click="load3">按'文物年份'查询</el-button>
    </div>
    <el-table :data="tableData" stripe style="width: 100%">
      <el-table-column prop="id" label="序号" sortable/>
      <el-table-column prop="name" label="文物名称" sortable/>
      <el-table-column prop="museum" label="藏馆"  />
      <el-table-column prop="date" label="文物年代" sortable/>

      <el-table-column fixed="right" >
        <template #default="scope">
          <el-button   @click="open(scope.row)" type="text">详情</el-button>

        </template>
      </el-table-column>

    </el-table>

    <div style="margin:10px">
      <el-pagination
          v-model:currentPage="currentPage"
          :page-sizes="[5,10,20]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
      >
      </el-pagination>




    </div>
  </div>


</template>

<script >

import request from "@/utils/request";

// import { ElMessage } from 'element-plus'
import { ElMessageBox, ElMessage } from 'element-plus'
// import type { Action } from 'element-plus'

export default {
  name: 'wen_wu',

  components: {},
  data() {
    return {
      // form对象属性跟数据库中一一对应，弹窗表格引进来的
      form: {},
      dialogVisible: false,
      total: 0,
      pageSize: 10,
      currentPage: 1,
      search: '',
      search1: '',
      search2: '',
      search3: '',
      num:'',
      // 表的变量
      tableData: []
    }
  },

  created() {
    this.load()
  },


  methods: {
    open(row){
      ElMessageBox.confirm(row.description,'文物信息',{
        confirmButtonText:'确定',
        cancelButtonText: '取消',
      })

    },
    load() {
      request.get("/wenwu", {
        params: {
          pageNum: this.currentPage,
          pageSize: this.pageSize,
          search: this.search1,
          num:1,
        },

      }).then(res => {
        // console.log(res)
        this.tableData = res.data.records,
            this.total = res.data.total
      })
    },
    load2() {
      request.get("/wenwu", {
        params: {
          pageNum: this.currentPage,
          pageSize: this.pageSize,
          search: this.search2,
          num:2,
        },

      }).then(res => {
        // console.log(res)
        this.tableData = res.data.records,
            this.total = res.data.total
      })
    },
    load3() {
      request.get("/wenwu", {
        params: {
          pageNum: this.currentPage,
          pageSize: this.pageSize,
          search: this.search3,
          num:3,
        },

      }).then(res => {
        // console.log(res)
        this.tableData = res.data.records,
            this.total = res.data.total
      })
    },

    handleSizeChange(pageSize) {   // 改变当前每页的个数触发
      this.pageSize = pageSize
      this.load()
    },
    handleCurrentChange(pageNum) {  // 改变当前页码触发
      this.currentPage = pageNum
      this.load()
    },
    // 打开新增弹窗
    add() {
      this.dialogVisible = true
      this.form = {}
    },

    save() {
      //更新
      if (this.form.id) {
        request.put("/wenwu", this.form).then(res => {
          console.log(res)
          if(res.code==0){
            ElMessage({
              type: 'success',
              message: '更新成功',
            })
          }else{
            ElMessage({
              type: 'error',
              message: 'res.msg',
            })
          }

          this.load()
          this.dialogVisible=false
        })
      } else {
        //新增
        request.post("/wenwu", this.form).then(res => {
          console.log(res)
          if(res.code==0){
            ElMessage({
              type: 'success',
              message: '新增成功',
            })
          }else{
            ElMessage({
              type: 'error',
              duration:2000,
            })
          }
          this.load()
          this.dialogVisible=false
        })
      }
    },
  }
}



</script>