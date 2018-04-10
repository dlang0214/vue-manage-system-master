<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 管理员管理</el-breadcrumb-item>
                <el-breadcrumb-item>角色管理</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div>
            <span>
                <el-button type="primary" @click="showAdd">新增角色</el-button>
            </span>
        </div>
        <el-table :data="chuData" border style="width: 100%;margin-top: 10px" ref="multipleTable"  height="520" v-loading.body="loading">
            <el-table-column prop="rId" label="权限id" sortable>
            </el-table-column>
            <el-table-column prop="rName" label="权限名" sortable>
            </el-table-column>
            <el-table-column prop="createDateStr" label="创建时间" sortable>
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
                        <span>角色名</span>
                        <span>
                            <el-input v-model="itemMess.rName" :disabled="able"></el-input>
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
                    :titles="['未获得权限','已获得权限']"
                    :props="{
                          key: 'pId',
                          label: 'pName'
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
                loading:false,
                tagNable:true,
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
//            this.getRoles();
        },
        watch:{

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
                this.itemMess = {};
                this.able=false;
                this.showUpdata=false;
                this.tagNable = false
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
                if(!mess.rName || mess.rName ==""){
                    self.$message("角色名不能为空");
                    return 0;
                }
                self.loading = true;
                $.ajax({
                    type:"post",
                    url:self.hrefLoction+'insertRole.json',
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
                self.loading = true
                var mess = {};
                mess.rId =self.itemMess.rId;
                mess.rName =self.itemMess.rName;
                console.log(mess);
                $.ajax({
                    type:"post",
                    url:self.hrefLoction+'updateRole.json',
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

            upRoles:function () {
                let self = this;
                var pram = [];
                console.log(self.itemRoles);
                for(var i=0;i<self.allRoles.length;i++){
                    if($.inArray(self.allRoles[i].pId,self.itemRoles)>=0){
                        console.log(self.allRoles[i].pId,self.itemRoles);
                        pram.push({
                            pId:self.allRoles[i].pId,isCheck:1
                        })
                    }else {
                        pram.push({
                            pId:self.allRoles[i].pId,isCheck:0
                        })
                    }

                }
                console.log(pram);
                self.url = self.hrefLoction+'updateRoleOfPremissione.json?rId='+self.itemMess.rId+'&listStr='+JSON.stringify(pram);
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
                self.url = self.hrefLoction+'findRole.json';
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.chuData=response.data.dataInfo.listData;//获取开始数据

                    }
                    else {
                        self.$message(response.data.message)
                    }

                }, (response) => {
                    console.log('error');
                });
            },

            getRoles:function () {
                let self = this;
//
                self.url = self.hrefLoction+'findPremission.json';
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.allRoles=response.data.dataInfo.listData;//获取开始数据
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
                this.tagNable = true;
//      封装删除数据 [{tagNumber:"30853 4",orderNumber:4,tableName:"PDA_INV_ITEM_ADDENDUM"}]
                this.deleteId = item.rId;
             },
            showRole:function (item) {
                this.roleItem  = true;
                this.itemRoles = [];
                this.itemMess = item;
                this.allRoles = item.cList;
                for(var i=0;i<item.cList.length;i++){
                    if(item.cList[i].isCheck == 1){
                        this.itemRoles.push(item.cList[i].pId);
                    }

                }
                console.log(this.allRoles);
                console.log(this.itemRoles);
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
