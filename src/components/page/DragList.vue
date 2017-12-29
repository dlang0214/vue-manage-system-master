<template>
    <section class="main">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-upload2"></i> pda人员管理</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="drag-box-left">
            <div class="plugins-tips">
                如果人员信息不在下面表里，则不能使用pda进行盘点，请联系财务人员<br>
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
            <el-table :data="tableData" border style="width: 100%;margin-top: 10px" ref="multipleTable"  height="520">
                <el-table-column prop="userNo" label="工号" sortable width="180">
                </el-table-column>
                <el-table-column prop="userName" label="姓名" sortable width="180">
                </el-table-column>
                <el-table-column prop="department" label="部门" sortable>
                </el-table-column>
                <el-table-column  label="盘点状态" sortable >
                    <template scope="scope">
                        <span v-if="scope.row.isFinanceUser == 1">复盘人员</span>
                        <span v-else>初盘人员</span>
                     </template>
                </el-table-column>
                <el-table-column label="操作" width="180">
                    <template scope="scope">

                        <el-button  @click="deletePer(scope.row.userNo)">删除</el-button>
                        <el-button  @click="updataPer(scope.row.userNo)">修改</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
    </section>
</template>

<script>
    export default {
        data() {
            return {
                selcont:"",
                select:"1",
                tableData:[],
                chuData:[],
                checkList:["0","1"]
            }
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
        created(){
            this.getData();//开始获取复盘的信息
        },
        methods: {
            getData(isFin){
                let self = this;
                self.url =self.hrefLoction+ 'findUSerInformation.json?';
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.chuData = response.data.dataInfo.listData;
                     self.tableData = self.chuData
                    console.log(self.tableData);
                }, (response) => {
                    console.log('error');
                });
            },
            deletePer:function () {

            },
            updataPer:function () {

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
            }
    }
    }
</script>


<style scoped>


</style>
