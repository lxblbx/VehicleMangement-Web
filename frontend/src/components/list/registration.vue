<template>
  <div>
    <h3>出车登记</h3>
    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" class="input-with-select">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>

        <el-col :span="4">
          <el-button type="primary" @click="addDialogVisible=true">添加出车信息</el-button>
        </el-col>
      </el-row>

      <el-table :data="userlist" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="调度单id" prop="dispatchId"></el-table-column>
        <el-table-column label="司机id" prop="driverId"></el-table-column>
        <el-table-column label="汽车id" prop="carId"></el-table-column>
        <el-table-column label="出车日期" prop="dispatchDate"></el-table-column>
        <el-table-column label="操作" width="180px">
         <template slot-scope="scope">
            <el-button 
              type="primary" 
              icon="el-icon-edit" 
              size="mini"
              @click="showEditDialog(scope.row.dispatchId)"
            ></el-button>
            <el-button 
              type="danger" 
              icon="el-icon-delete" 
              size="mini"
              @click="removeTransportationById(scope.row.dispatchId)"
            ></el-button>
            <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>

    <!--添加出车信息弹出来的对话框-->
    <el-dialog
      title="添加订单"
      :visible.sync="addDialogVisible"
      width="50%"
      @close="addDialogClosed"
    >
      <!--form表单-->
      <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
        <el-form-item label="调度单id" prop="dispatchId">
          <el-select v-model="addForm.dispatchId" placeholder="请选择">
            <el-option
              v-for="item in dispatchoptions"
              :key="item.dispatchId"
              :label="item.dispatchId"
              :value="item.dispatchId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="司机id" prop="driverId">
          <el-select v-model="addForm.driverId" placeholder="请选择">
            <el-option
              v-for="item in driveroptions"
              :key="item.driverId"
              :label="item.driverId"
              :value="item.driverId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="汽车id" prop="carId">
          <el-select v-model="addForm.carId" placeholder="请选择">
            <el-option
              v-for="item in caroptions"
              :key="item.carId"
              :label="item.carId"
              :value="item.carId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="出车日期" prop="dispatchDate">
          <el-input v-model="addForm.dispatchDate"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible= false">取 消</el-button>
        <el-button type="primary" @click="addTransportation()">确 定</el-button>
      </span>
    </el-dialog>

    <!--编辑出车信息弹出来的对话框-->
    <el-dialog
      title="添加订单"
      :visible.sync="editDialogVisible"
      width="50%"
      @close="editDialogClosed"
    >
      <!--form表单-->
      <el-form :model="editForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
        <el-form-item label="调度单id" prop="dispatchId">
          <el-select v-model="editForm.dispatchId" placeholder="请选择">
            <el-option
              v-for="item in dispatchoptions"
              :key="item.dispatchId"
              :label="item.dispatchId"
              :value="item.dispatchId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="司机id" prop="driverId">
          <el-select v-model="editForm.driverId" placeholder="请选择">
            <el-option
              v-for="item in driveroptions"
              :key="item.driverId"
              :label="item.driverId"
              :value="item.driverId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="汽车id" prop="carId">
          <el-select v-model="editForm.carId" placeholder="请选择">
            <el-option
              v-for="item in caroptions"
              :key="item.carId"
              :label="item.carId"
              :value="item.carId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="出车日期" prop="dispatchDate">
          <el-input v-model="editForm.dispatchDate"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible= false">取 消</el-button>
        <el-button type="primary" @click="editTransportation()">确 定</el-button>
      </span>
    </el-dialog>

  </div>
</template>

<script>
export default {
  data() {
    return {
      queryInfo: {
        query: "",
        pagenum: 1,
        pagesize: 2
      },
      dispatchqueryInfo: {
        query: "",
        pagenum: 1,
        pagesize: 2
      },
      driverqueryInfo: {
        query: "",
        pagenum: 1,
        pagesize: 2
      },
      carqueryInfo: {
        query: "",
        pagenum: 1,
        pagesize: 2
      },
      userlist: [],
      totol: 0,
      //添加对话框显示
      addDialogVisible: false,
      editDialogVisible: false,
      //添加用户表单数据
      addForm: {
        dispatchId: "",
        driverId: "",
        carId: "",
        dispatchDate: ""
      },
      editForm:{},
      //添加表单验证规则
      addFormRules: {
        dispatchId:[
          {required: true, message:"请选择调度单id", trigger:"blur"}
        ]
      },
      dispatchoptions:[],
      driveroptions:[],
      caroptions:[]
    };
  },
  created() {
    this.getUserList();
    this.getDispatchList();
    this.getDriverList();
    this.getCarList();
    
  },
  methods: {
    getUserList() {
      const _this = this;
      this.$http
        .get("/registrationinfo", {
          params: this.queryInfo
        })
        .then(res => {
          _this.userlist = res.data;
        });
    },
    // 获取所有车辆
    getCarList() {
      const _this = this;
      this.$http
        .get("/carinfo", {
          params: this.carqueryInfo
        })
        .then(res => {
          // console.log(res.data);
          _this.caroptions = res.data;
          // console.log(_this.userlist);
        });
    },
    // 获取所有司机
    getDriverList() {
      const _this = this;
      this.$http
        .get("/driverinfo", {
          params: this.driverqueryInfo
        })
        .then(res => {
          // console.log(res.data);
          _this.driveroptions = res.data;
          // console.log(_this.userlist);
        });
    },
    // 获取所有调度单
    getDispatchList() {
      const _this = this;
      this.$http
        .get("/dispatchinfo", {
          params: this.dispatchqueryInfo
        })
        .then(res => {
          console.log(res.data);
          _this.dispatchoptions = res.data;
          console.log(_this.userlist);
        });
    },
    addDialogClosed() {
      this.$refs.addFormRef.resetFields();
    },
    editDialogClosed(){
      this.$refs.editFormRef.resetFields();
    },
    //显示编辑对话框
    async showEditDialog(id){
      this.getDispatchList();
      this.getDriverList();
      this.getCarList();
      const _this=this;
      await this.$http.get("/registrationinfo/edit/" + id).then(res=>{
        _this.editForm = res.data;
      });
      this.editDialogVisible = true;
    },
    //添加出车信息
    addTransportation() {
      const _this=this;
      this.$http.post("/registrationinfo/addregistrationinfo",this.addForm).then(res =>{
        if(res.data == "成功"){
          _this.$message.success("添加成功");
          _this.addDialogVisible = false;
          // 重新获取表数据
          _this.getUserList();
        }else{
          _this.$message.error("添加失败");
        }
      });
    },
    // 编辑出车信息确认
    editTransportation(){
      const _this=this;
      this.$http.post("/registrationinfo/editinfo", this.editForm).then(res =>{
        if(res.data == "成功"){
          console.log(res.data);
          _this.$message.success("修改成功");
          _this.editDialogVisible = false;
          _this.getUserList();
        }else{
          console.log(res.data);
          _this.$message.error("添加失败");
        }
      });
    },
    //删除出车信息
    removeTransportationById(id){
      const _this=this;
      this.$confirm("此操作将永久删除该出车信息, 是否继续?","提示",{
        confirmButtonText:"确定",
        cancelButtonText:"取消",
        type:"warning"
      }).then(()=>{
        this.$http.delete("registrationinfo/delete/" + id).then(res=>{
          if(res.data == "成功"){
            this.$message({
              type:"success",
              message:"删除成功!"
            });
            _this.getUserList();
          }else{
            this.$message({
              type:"info",
              message:"删除失败"
            });
          }
        });
      }).catch(()=>{
        this.$message({
          type:"info",
          message:"已取消删除"
        });
      });
    }
  }
};
</script>

<style lang="less" scoped>
</style>