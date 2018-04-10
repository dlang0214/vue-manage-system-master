<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 管理员管理</el-breadcrumb-item>
                <el-breadcrumb-item>角色管理</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div style="width:100%;display: inline-flex;align-items: center">
                     <span >
                            条件查询：
                    </span>
            <el-input placeholder="请输入内容" v-model="selcont" style="width: 50%" @change="selectPer">
                <el-select v-model="select" slot="prepend" placeholder="请选择" style="width:80px" >
                    <el-option label="工号" value="1"></el-option>
                    <el-option label="姓名" value="2"></el-option>
                    <el-option label="部门" value="3"></el-option>
                </el-select>
                <el-button slot="append" icon="search"></el-button>
            </el-input>
            <el-checkbox-group v-model="checkList" style="margin-left: 30px">
                <el-checkbox label="0">初盘人员</el-checkbox>
                <el-checkbox label="1">复盘人员</el-checkbox>
            </el-checkbox-group>

        </div>
        <div>
            <span>
                <el-button type="primary" @click="showAdd">新增角色</el-button>
            </span>
        </div>
        <el-table :data="tableData" border style="width: 100%;margin-top: 10px" ref="multipleTable"  height="520" v-loading.body="loading">
            <el-table-column prop="userNo" label="工号" sortable>
            </el-table-column>
            <el-table-column prop="userName" label="姓名" sortable>
            </el-table-column>
            <el-table-column prop="department" label="盘点部门" sortable>
            </el-table-column>
            <el-table-column  label="盘点状态" sortable >
                <template slot-scope="scope">
                    <span v-if="scope.row.isFinanceUser == 1">复盘人员</span>
                    <span v-else>初盘人员</span>
                </template>
            </el-table-column>
            <el-table-column label="操作" width="220">
                <template slot-scope="scope">
                    <el-button size="small" @click="showItem(scope.$index,scope.row)">查看详情</el-button>
                    <el-button size="small" @click="showRole(scope.row)">设置权限</el-button>
                </template>
            </el-table-column>

        </el-table>
        <div class="bgmess" v-show="bgItem">
            <div style="position: relative">
                <div class="bgItemclose" @click="bgItem=false">
                        <i class="el-icon-close"></i>
                </div>
                <ul>
                    <li>
                        <span>工号</span>
                        <span>
                            <el-input v-model="itemMess.userNo" :disabled="addable"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>姓名</span>
                        <span>
                            <el-input v-model="itemMess.userName" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>部门</span>
                        <span>
                            <el-input v-model="itemMess.department" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>密码</span>
                        <span>
                            <el-input v-model="itemMess.pwd" :disabled="addable" type="password"></el-input>
                        </span>
                    </li>

                    <li>
                        <span>初盘/复盘</span>
                        <span>
                            <el-switch
                                :disabled="able"
                                v-model="itemMess.isFinanceUser"
                                on-text="初"
                                off-text="复"
                                on-value="0"
                                off-value="1"
                                on-color="#13ce66"
                                off-color="#ff4949">
                            </el-switch>

                        </span>
                    </li>
                    <li style="display: flex;justify-content: center" v-show="showUpdata">
                        <el-button type="primary" @click="updateItem" >修改</el-button>
                        <el-button type="primary" @click="deleteItem">删除</el-button>
                    </li>
                    <li style="display: flex;justify-content: center" v-show="!showUpdata">
                        <el-button type="primary" @click="sub(updoradd)">确定</el-button>
                        <el-button type="primary" @click="removeUp">取消</el-button>
                    </li>
                </ul>

            </div>

        </div>
        <div class="bgmess" v-show="roleItem">
            <div style="position: relative">
                <div class="bgItemclose" @click="roleItem=false">
                        <i class="el-icon-close"></i>
                </div>
                <el-transfer
                    v-model="itemRoles"
                    :titles="['未获得角色','已获得角色']"
                    :props="{
                          key: 'rId',
                          label: 'rName'
                        }"
                    :data="allRoles">
                </el-transfer>
                <el-button type="primary" @click="upRoles">确定修改</el-button>
            </div>

        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    import ElButton from "../../../node_modules/element-ui/packages/button/src/button.vue";
    import ElInput from "../../../node_modules/element-ui/packages/input/src/input.vue";

    export default {
        components: {
            ElInput,
            ElButton},
        data: function(){
            return {
                selcont:"",
                select:"1",
                tableData:[],
                checkList:["0","1"],
                loading:false,
                addable:true,
                able:true,//修改内容是否可编辑
                showUpdata:true,//显示修改or确定
                chuData:[],//获取的数据
                itemMess:{},
                updoradd:"up",
                bgItem:false,
                roleItem:false,
                deleteId:"", //删除的ID
                allRoles:[],
                itemRoles:[],
            }
        },
        created(){
            this.getchuData(1);//开始获取复盘的信息
        },
        watch:{
            checkList:function (val, oldVal) {
                var m=[]
                var vm =this;
                console.log(val);
                if(val.toString() == "0"){
                    console.log("0");
                    for (var i in vm.chuData){
                        if((vm.chuData[i].isFinanceUser == "0")){
                            m.push(vm.chuData[i])
                        }
                    }
                    vm.tableData=m;
                }else if(val.toString() == "1"){
                    for (var i in vm.chuData){
                        if((vm.chuData[i].isFinanceUser == "1")){
                            m.push(vm.chuData[i])
                        }
                    }
                    vm.tableData=m;
                }else {
                    vm.tableData = vm.chuData;
                }
            }
        },
        methods: {
            //设置表格
            tableRowClassName(row, index) {
                if (row.isFinance === "未初盘点") {
                    return 'info-row';
                }
                return '';
            },
//          取消提交
            removeUp:function () {
                this.showUpdata = true
                this.able = true;
            },
            showAdd:function () {
                this.updoradd = "add";
                this.bgItem = true;
                this.itemMess = { isFinanceUser:"0"};
                this.able=false;
                this.showUpdata=false;
                this.addable = false
            },
            sub:function (type) {
                if(type == 'up'){
                    this.updatepost();
                }else {
                    this.addpost();
                }
            },
//            新增接口调用
            addpost:function () {
                let self = this;
                var mess = self.itemMess;
                mess.account = self.itemMess.userNo;
                mess.password = self.itemMess.pwd;
                mess.createUser = JSON.parse(localStorage.getItem("pdaUserMess")).userNo;
                mess.department = self.itemMess.department;
                mess.isFinanceUser = self.itemMess.isFinanceUser;
                mess.userName = self.itemMess.userName;

                if(!mess.account || mess.account ==""){
                    self.$message("工号不能为空");
                    return 0;
                }
                self.loading = true;
                $.ajax({
                    type:"post",
                    url:self.hrefLoction+'register.json',
                    dataType:"json",
                    data: mess,
                    success:function(data){
                        console.log(data);
                        self.$message(data.message);
                        if(data.statusCode == 0){
                            self.bgItem = false;
                            self.getchuData(1);
                        }
                    },
                    error:function (data) {
                        self.$message( {type: 'error',data:"请求失败"});
                    },
                    complete:function () {
                        self.loading=false;
                    }
                });
            },
//            修改接口调用
            updatepost:function () {
                let self = this;
                self.loading = true;
                var mess = {};
                mess.account=self.itemMess.userNo;
                mess.department=self.itemMess.department;
                mess.userName=self.itemMess.userName;
                mess.isFinanceUser=self.itemMess.isFinanceUser;
                console.log(mess);
                $.ajax({
                    type:"post",
                    url:self.hrefLoction+'updateUserInfor.json',
                    dataType:"json",
                    data: mess,
                    success:function(data){
                        console.log(data);
                        self.$message(data.message);
                        if(data.statusCode == 0){
                            self.bgItem = false;
                        }
                    },
                    error:function (data) {
                        self.$message( {type: 'error',data:"请求失败"});
                    },
                    complete:function () {
                        self.loading=false;
                    }
                });
            },
            deleteMess:function(mess){
                let self = this;
                self.loading = true;
                self.url = self.hrefLoction+'deleteRole.json?rId='+self.itemMess.rId;
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.$message(response.data.message);
                        self.bgItem = false;
                        self.getchuData(1);
                    }
                    else {
                        self.$message(response.data.message)
                    }

                }, (response) => {
                    console.log('error');
                });
            },
//            删除按钮点击事件
            deleteItem:function () {
                this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                   this.deleteMess(this.deleteId)
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            selectPer:function () {
                var vm =this;
                var m=[];
                console.log( vm.chuData);
                if(vm.chuData.length<1){
                    return 0
                }
                if(vm.select=="1"){//根据工号查询
                    for (var i in vm.chuData){
                        if(new RegExp(vm.selcont).test(vm.chuData[i].userNo)){
                            m.push(vm.chuData[i])
                        }
                    }
                }
                if(vm.select=="2"){//根据姓名查询
                    for (var i in vm.chuData){
                        if(new RegExp(vm.selcont).test(vm.chuData[i].userName)){
                            m.push(vm.chuData[i])
                        }
                    }
                }
                if(vm.select=="3"){//根据姓名查询
                    for (var i in vm.chuData){
                        if(new RegExp(vm.selcont).test(vm.chuData[i].department)){
                            m.push(vm.chuData[i])
                        }
                    }
                }
                vm.tableData=m;
            },
            upRoles:function () {
                let self = this;
                var pram = [];
                console.log(self.itemRoles);
                for(var i=0;i<self.allRoles.length;i++){
                    if($.inArray(self.allRoles[i].rId,self.itemRoles)>=0){
                        console.log(self.allRoles[i].rId,self.itemRoles);
                        pram.push({
                            rId:self.allRoles[i].rId,isCheck:1
                        })
                    }else {
                        pram.push({
                            rId:self.allRoles[i].rId,isCheck:0
                        })
                    }

                }
                console.log(pram);
                self.url = self.hrefLoction+'updateUserRole.json?userNo='+self.itemMess.userNo+'&listStr='+JSON.stringify(pram);
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.$message(response.data.message);
                        this.roleItem  = false;
                        self.getchuData();

                    }
                    else {
                        self.$message(response.data.message)
                    }

                }, (response) => {
                    console.log('error');
                });
            },

            getchuData(page){
                let self = this;
//
                self.url = self.hrefLoction+'findUSerInformation.json';
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.chuData=response.data.dataInfo.listData;//获取开始数据
                        self.tableData =self.chuData;
                    }
                    else {
                        self.$message(response.data.message)
                    }

                }, (response) => {
                    console.log('error');
                });
            },

//          点击修改按钮，出现确定和取消按钮
            updateItem:function () {
                this.able = false;
                this.showUpdata = false;
            },

// 相信信息出现
            showItem:function(index,item){
                this.updoradd = "up";
                this.itemMess = item;
                console.log(this.itemMess);
                this.bgItem = true;
                this.able=true;
                this.showUpdata=true;
                this.addable = true;
//      封装删除数据 [{tagNumber:"30853 4",orderNumber:4,tableName:"PDA_INV_ITEM_ADDENDUM"}]
                this.deleteId = item.rId;
             },
            showRole:function (item) {
                let self = this;
                this.itemMess = item;
                self.url = self.hrefLoction+'findUserRole.json?userNo='+item.userNo;
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.roleItem  = true;
                        self.itemRoles = [];
                        console.log(response.data.dataInfo.listData);
                        self.allRoles =response.data.dataInfo.listData;
                        for(var i=0;i<self.allRoles.length;i++){
                            if(self.allRoles[i].isCheck == 1){
                                this.itemRoles.push(self.allRoles[i].rId);
                            }
                        }
                        console.log(this.allRoles);
                        console.log(this.itemRoles);
                    }
                    else {
                        self.$message(response.data.message)
                    }

                }, (response) => {
                    console.log('error');
                });
            }

        },
        computed:{

        },
        beforeMount(){

        }
    }
</script>

<style scoped>
    .table .el-input-group__prepend {

        width: 80px;

    }
    .el-table .info-row {
        background: #c9e5f5;
    }

    .bgmess{
        position: fixed;
        top:70px;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0,0,0,0.5);
        z-index: 999;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #555;


    }

    .bgmess ul{
        padding: 10px;
        min-width: 300px;
        border: solid 1px #ffffff;
        border-radius: 10px;
        position: relative;
        background-color: #fff;
    }
    .bgmess ul li{
        display: flex;
        height: 45px;
        line-height: 45px;
        border-bottom: solid 1px #eee;
    }
    .bgmess ul li span:first-child{
        width: 100px;
    }
    .bgmess ul li span:last-child{
        flex: 1;
    }
    .bgItemclose{
        position: absolute;
        top: -45px;
        right: 0;
        width: 35px;
        height: 35px;
        border: solid 2px #fff;
        border-radius: 50%;

        color: #ffffff;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 15px;
    }
</style>
