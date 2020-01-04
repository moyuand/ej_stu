<template>
<!-- 顾客管理 -->
  <div>
      <!-- 标签页 -->
    <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="所有订单" name="first">
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="orders.list">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="orderTime" label="下单时间"></el-table-column>
      <el-table-column prop="total" label="总价"></el-table-column>
      <el-table-column prop="customerId" label="顾客id"></el-table-column>
      <el-table-column prop="waiterId" label="员工id"></el-table-column>
      <el-table-column prop="addressId" label="地址id"></el-table-column>
      <el-table-column prop="status" label="状态"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <!-- slot表示表格数据内容 -->
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="orders.total" @current-change="pageChangeHandler"></el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      title="录入订单信息"
      :visible.sync="visible"
      width="60%">
        ---{{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="真实姓名">
          <el-input v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone"></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->
        </el-tab-pane>
        <el-tab-pane label="待支付" name="second">配置管理</el-tab-pane>
        <el-tab-pane label="待派单" name="third">角色管理</el-tab-pane>
        <el-tab-pane label="待接单" name="fourth">定时任务补偿</el-tab-pane>
        <el-tab-pane label="待服务" name="fifth">定时任务补偿</el-tab-pane>
        <el-tab-pane label="待确认" name="sixth">定时任务补偿</el-tab-pane>
        <el-tab-pane label="已完成" name="seventh">定时任务补偿</el-tab-pane>        
    </el-tabs>
  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  methods:{
    loadData(){
      let url = "http://localhost:6677/order/queryPage"
      request({
          url,
          method:"post",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data:querystring.stringify(this.params)
      }).then((response)=>{
            this.orders=response.data;
      })
    },
    pageChangeHandler(page){
        this.params.page=page-1;
        this.loadData();
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'order',age:12}
      // json字符串 '{"type":"order","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=order&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/order/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // alert(id)
        let url="http://localhost:6677/order/deleteById?id="+id
        request.get(url).then((response)=>{
          this.loadData();
          this.$message({
          type: 'success',
          message: '删除成功!'
          });
        })        
      })     
    },
    toUpdateHandler(row){
      this.form=row;
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.form={
        type:"order"
      }
      this.visible = true;
    },
    handleClick(tab, event) {
        console.log(tab, event);
      }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      orders:{},
      form:{
        type:"order"
      },
      params:{
          page:0,
          pageSize:10
      },
      activeName: 'first'
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  }
}
</script>

<style scoped>
 
</style>