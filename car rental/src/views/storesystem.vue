<template>
  <div style="width: 88%; margin-top: 10px">
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item>
        <el-select v-model="formInline.Queryitem" placeholder="查询项">
          <el-option label="店名" value="shopname"></el-option>
          <el-option label="店长" value="shopmanager"></el-option>
          <!-- <el-option label="性别" value="sex"></el-option>
                <el-option label="联系方式" value="phone"></el-option>
                <el-option label="邮箱地址" value="email"></el-option>
                <el-option label="用户权限" value="administrator"></el-option> -->
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="formInline.message" placeholder="请输入查询内容"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="Query">查询</el-button>
        <el-button type="info" @click="cancelQuery">清空</el-button>
        <el-button type="primary" @click="addshop">添加</el-button>
      </el-form-item>
    </el-form>

    <el-table fit :data="shopList" style="width: 80%; margin-left: 20px">
      <el-table-column disable prop="shopname" label="店名" width="100">
      </el-table-column>
      <el-table-column prop="shopmanager" label="店长" width="100"> </el-table-column>
      <el-table-column prop="clerk" label="店员数" width="80"> </el-table-column>
      <el-table-column prop="cars" label="车辆数" width="80"> </el-table-column>
      <el-table-column prop="profit" label="利润（万元）" width="80"> </el-table-column>
      <el-table-column prop="businesstimebegin" label="营业开始时间" width="120">
      </el-table-column>
      <el-table-column prop="businesstimeend" label="营业结束时间" width="120">
      </el-table-column>
      <el-table-column prop="tel" label="电话" width="120"> </el-table-column>
      <el-table-column prop="address" label="地址" width="120"> </el-table-column>
      <el-table-column label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            type="primary"
            size="small"
            @click="userEdit(scope.$index, scope.row)"
            >编辑</el-button
          >
          <el-button type="danger" size="small" @click="userDelete(scope.row)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>

    <el-dialog
      title="编辑门店信息"
      :visible="shopEditForm"
      :modal-append-to-body="false"
      @close="closeDialog"
    >
      <el-form ref="editsForm" :model="editsForm" label-width="80px">
        <el-form-item label="店名">
          <el-input
            v-model="editsForm.shopname"
            max-length="12"
            disabled="disabled"
          ></el-input>
        </el-form-item>
        <el-form-item label="店长">
          <el-input v-model="editsForm.shopmanager"></el-input>
        </el-form-item>
        <el-form-item label="店员数">
          <el-input
            v-model="editsForm.clerk"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="车辆数">
          <el-input
            v-model="editsForm.cars"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="利润">
          <el-input
            v-model="editsForm.profit"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>

        <el-form-item label="营业开始时间">
          <el-time-picker
            v-model="editsForm.businesstimebegin"
            type="time"
            format="HH:mm:ss"
            value-format="HH:mm:ss"
            placeholder="选择日期时间"
          >
          </el-time-picker>
        </el-form-item>
        <el-form-item label="营业结束时间">
          <el-time-picker
            v-model="editsForm.businesstimeend"
            type="time"
            format="HH:mm:ss"
            value-format="HH:mm:ss"
            placeholder="选择日期时间"
          >
          </el-time-picker>
        </el-form-item>
        <el-form-item label="电话">
          <el-input
            v-model="editsForm.tel"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="editsForm.address"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="UserModifyEdit()">确定</el-button>
          <el-button @click="shopEditForm = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>

    <el-dialog
      title="添加门店信息"
      :visible="shopAddForm"
      :modal-append-to-body="false"
      @close="closeDialog"
    >
      <el-form ref="addForm" :model="addForm" label-width="100px">
        <el-form-item label="店名">
          <el-input v-model="addForm.shopname" max-length="12"></el-input>
        </el-form-item>
        <el-form-item label="店长">
          <el-input v-model="addForm.shopmanager"></el-input>
        </el-form-item>
        <el-form-item label="店员数">
          <el-input
            v-model="addForm.clerk"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="车辆数">
          <el-input
            v-model="addForm.cars"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="利润（万元）">
          <el-input
            v-model="addForm.profit"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>

        <el-form-item label="营业开始时间">
          <el-time-picker
            v-model="addForm.businesstimebegin"
            type="time"
            format="HH:mm:ss"
            value-format="HH:mm:ss"
            placeholder="选择日期时间"
          >
          </el-time-picker>
        </el-form-item>
        <el-form-item label="营业结束时间">
          <el-time-picker
            v-model="addForm.businesstimeend"
            type="time"
            format="HH:mm:ss"
            value-format="HH:mm:ss"
            placeholder="选择日期时间"
          >
          </el-time-picker>
        </el-form-item>
        <el-form-item label="电话">
          <el-input
            v-model="addForm.tel"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="addForm.address"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="shopAddEdit()">确定</el-button>
          <el-button @click="shopAddForm = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "ShopInfo",
  data() {
    return {
      formInline: {
        message: "", //查询内容
        Queryitem: "", //查询项
      },
      shopAddForm: false,
      addForm: {
        id: "",
        shopname: "",
        shopmanager: "",
        clerk: "",
        cars: "",
        profit: "",
        businesstimebegin: "",
        businesstimeend: "",
        tel: "",
        address: "",
      },
      shopList: [],
      shopEditForm: false,
      modifyindex: 0,
      editsForm: {
        id: "",
        shopname: "",
        shopmanager: "",
        clerk: "",
        cars: "",
        profit: "",
        businesstimebegin: "",
        businesstimeend: "",
        tel: "",
        address: "",
      },
    };
  },
  methods: {
    shopAddEdit() {
      let addshop = this.addForm;
      let {
        shopname,
        shopmanager,
        clerk,
        cars,
        profit,
        businesstimebegin,
        businesstimeend,
        tel,
        address,
      } = addshop;
      if (
        shopname == "" ||
        shopmanager == "" ||
        clerk == "" ||
        cars == "" ||
        profit == "" ||
        businesstimebegin == "" ||
        businesstimeend == "" ||
        tel == "" ||
        address == ""
      ) {
        this.$message.error("内容每一项都不准为空");
      } else {
        axios.post("http://localhost:3000/shoplist/", addshop).then((res) => {
          console.log("add  res:" + res);
          // console.log(res.data.data);
          // if(res.data.data ===1){
          this.getshopList();
          this.shopAddForm = false;
          this.$message("添加成功");
          this.reset();
          // }
        });
      }
    },

    getshopList() {
      axios.get("http://localhost:3000/shoplist/").then((res) => {
        const { status, data } = res;
        if (status === 200) {
          this.shopList = data;
        }
      });
    },
    closeDialog() {
      this.shopEditForm = false;
      this.shopAddForm = false;
    },
    userEdit(index, row) {
      this.shopEditForm = true; //编辑信息模态框显示
      this.editsForm = Object.assign({}, row); //  获得所有数据显示在编辑信息模态框里面
      this.modifyindex = index;
    },
    UserModifyEdit() {
      let modifyuser = this.editsForm;
      console.log(modifyuser);
      let { shopmanager, clerk, cars, profit, businesstime, tel, address } = modifyuser;
      if (
        shopmanager == "" ||
        clerk == "" ||
        cars == "" ||
        profit == "" ||
        businesstime == "" ||
        tel == "" ||
        address == ""
      ) {
        this.$message.error("修改内容除了不可编辑的每一项都不准为空");
      } else {
        axios
          .patch("http://localhost:3000/shoplist/" + modifyuser.id, modifyuser)
          .then((res) => {
            console.log("modify  res:" + res);

            // console.log(res.data.data);
            // if(res.data.data ===1){
            this.getshopList();
            this.shopEditForm = false;
            this.$message("修改成功");
            // }
          });
      }
    },
    addshop() {
      console.log("addshop");
      this.shopAddForm = true; //编辑信息模态框显示
    },
    userDelete(row) {
      this.$confirm("删除, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(axios.delete("http://localhost:3000/shoplist/" + row.id))
        .then((res) => {
          console.log("delete  res:" + res);
          this.getshopList();
          this.$message("删除成功");
        });
    },
    Query() {
      if (this.formInline.message === "") {
        this.getshopList();
        return;
      }
      let that = this;
      axios
        .get(
          "http://localhost:3000/shoplist?" +
            this.formInline.Queryitem +
            "=" +
            this.formInline.message
        )
        .then(function (res) {
          const { status, data } = res;
          if (status === 200) that.shopList = data;
        }) // 成功时的回调
        .catch(function (error) {
          console.log("error:" + error);
        });
    },
    cancelQuery() {
      this.formInline.message = "";
      this.getshopList();
    },
    reset() {
      this.shopname= "",
      this.shopmanager= "",
      this.clerk= "",
      this.cars= "",
      this.profit= "",
      this.businesstimebegin= "",
      this.businesstimeend= "",
      this.tel= "",
      this.address= ""
    }
  },

  mounted() {
    this.getshopList();
  },
};
</script>

<style scoped></style>
