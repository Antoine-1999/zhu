<template>
  <div style="width: 88%; margin-top: 10px">
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item>
        <el-select v-model="formInline.Queryitem" placeholder="查询项">
          <el-option label="姓名" value="name"></el-option>
          <!-- <el-option label="店长" value="shopmanager"></el-option> -->
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
        <el-button type="primary" @click="addstaff">添加</el-button>
      </el-form-item>
    </el-form>

    <el-table fit :data="staffList" style="width: 80%; margin-left: 20px">
      <el-table-column disable prop="name" label="姓名" width="200"> </el-table-column>
      <el-table-column prop="phone" label="电话" width="200"> </el-table-column>
      <el-table-column prop="money" label="工资" width="100"> </el-table-column>
      <el-table-column prop="address" label="地址" width="200"> </el-table-column>
      <el-table-column prop="email" label="电子邮箱" width="200"> </el-table-column>
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
      title="编辑员工信息"
      :visible="staffEditForm"
      :modal-append-to-body="false"
      @close="closeDialog"
    >
      <el-form ref="editsForm" :model="editsForm" label-width="80px">
        <el-form-item label="姓名">
          <el-input v-model="editsForm.name" max-length="12"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input
            v-model="editsForm.phone"
            onkeyup="value=value.replace(/^[1234567890][1234567890]\d{9}$/)"
          ></el-input>
        </el-form-item>
        <el-form-item label="工资">
          <el-input
            v-model="editsForm.money"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="editsForm.address" max-length="12"></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input
            v-model="editsForm.email"
            onkeyup="value=value.replace(/^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/)"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="UserModifyEdit()">确定</el-button>
          <el-button @click="staffEditForm = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>

    <el-dialog
      title="添加员工信息"
      :visible="staffAddForm"
      :modal-append-to-body="false"
      @close="closeDialog"
    >
      <el-form ref="addForm" :model="addForm" label-width="100px">
        <el-form-item label="姓名">
          <el-input v-model="addForm.name" max-length="12"></el-input>
        </el-form-item>
        <el-form-item label="电话">
          <el-input
            v-model="addForm.phone"
            onkeyup="value=value.replace(/^[1234567890][1234567890]\d{9}$/)"
          ></el-input>
        </el-form-item>
        <el-form-item label="工资">
          <el-input
            v-model="addForm.money"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input
            v-model="addForm.address"
            onkeyup="value=value.replace(/[^\d]/g,'')"
          ></el-input>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="staffAddEdit()">确定</el-button>
          <el-button @click="staffAddForm = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "StaffInfo",
  data() {
    return {
      formInline: {
        message: "", //查询内容
        Queryitem: "", //查询项
      },
      staffAddForm: false,
      addForm: {
        id: "",
        name: "",
        phone: "",
        money: "",
        address: "",
        email: "",
      },
      staffList: [],
      staffEditForm: false,
      modifyindex: 0,
      editsForm: {
        id: "",
        name: "",
        phone: "",
        money: "",
        address: "",
        email: "",
      },
    };
  },
  methods: {
    reset() {
      this.staffAddForm.name = "";
      this.staffAddForm.phone = "";
      this.editsForm.money = "";
      this.editsForm.address = "";
      this.editsForm.email = "";
    },
    staffAddEdit() {
      let addstaff = this.addForm;
      let { name, phone, money, address, email } = addstaff;
      if (name == "" || phone == "" || money == "" || address == "" || email == "") {
        this.$message.error("内容每一项都不准为空");
      } else {
        axios.post("http://localhost:3000/stafflist/", addstaff).then((res) => {
          console.log("add  res:" + res);
          // console.log(res.data.data);
          // if(res.data.data ===1){
          this.getstaffList();
          this.staffAddForm = false;
          this.$message("添加成功!!");
          this.reset();
          // }
        });
      }
    },

    getstaffList() {
      axios.get("http://localhost:3000/stafflist/").then((res) => {
        const { status, data } = res;
        if (status === 200) {
          this.staffList = data;
        }
      });
    },
    closeDialog() {
      this.staffEditForm = false;
      this.staffAddForm = false;
    },
    userEdit(index, row) {
      this.staffEditForm = true; //编辑信息模态框显示
      this.editsForm = Object.assign({}, row); //  获得所有数据显示在编辑信息模态框里面
      this.modifyindex = index;
    },
    UserModifyEdit() {
      let modifyuser = this.editsForm;
      console.log(modifyuser);
      let { name, phone, money, address, email } = modifyuser;
      if (name == "" || phone == "" || money == "" || address == "" || email == "") {
        this.$message.error("修改内容除了不可编辑的每一项都不准为空");
      } else {
        axios
          .patch("http://localhost:3000/stafflist/" + modifyuser.id, modifyuser)
          .then((res) => {
            console.log("modify  res:" + res);

            // console.log(res.data.data);
            // if(res.data.data ===1){
            this.getstaffList();
            this.staffEditForm = false;
            this.$message("修改成功");
            this.reset();
            // }
          });
      }
    },
    addstaff() {
      console.log("addstaff");
      this.staffAddForm = true; //编辑信息模态框显示
    },
    userDelete(row) {
      this.$confirm("删除, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(axios.delete("http://localhost:3000/stafflist/" + row.id))
        .then((res) => {
          console.log("delete  res:" + res);
          this.getstaffList();
          this.$message("删除成功");
          // }
        });
      // this.$axios.post('/deleteuser',row).then(res=>{
      //     if(res.data.data ===1){
      //         this.getuserList();
      //     }
      // })
    },
    Query() {
      if (this.formInline.message === "") {
        this.getstaffList();
        return;
      }
      //let keyarr = Object.keys(this.editsForm)
      //let i
      // keyarr.forEach((value,index) => {
      //     if(this.formInline.Queryitem.toString() === value.toString())
      //     {i = index}
      // })
      // let query = {
      //     [keyarr[i]]:this.formInline.message
      // }
      let that = this;
      axios
        .get(
          "http://localhost:3000/stafflist?" +
            this.formInline.Queryitem +
            "=" +
            this.formInline.message
        )
        .then(function (res) {
          const { status, data } = res;
          if (status === 200) that.staffList = data;
        }) // 成功时的回调
        .catch(function (error) {
          console.log("error:" + error);
        });
    },
    cancelQuery() {
      this.formInline.message = "";
      this.getstaffList();
    },
  },

  mounted() {
    this.getstaffList();
  },
};
</script>

<style scoped></style>
