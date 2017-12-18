<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>月度盘点汇总</el-breadcrumb-item>
            </el-breadcrumb>

        </div>
        <span style="position: absolute;top: 20px;right: 40px">
               <el-switch
                   v-model="iszi"
                   on-color="#13ce66"
                   off-color="#ff4949" >
          </el-switch>
            <span v-if="iszi==true" style="padding-left: 10px;padding-top: 4px" >资管</span>
            <span v-else style="padding-left: 10px;padding-top: 4px">列管</span>

            选择年：
                <el-date-picker
                    v-model="thisyear"
                    align="right"
                    type="year"
                    placeholder="选择年" @change="selectPer">
        </el-date-picker>

        </span>
        <div>

            <span style="width:55%;display: inline-block">
                条件查询：
            <el-input placeholder="请输入资产编号" v-model="selcont" style="width: 80%" >

                <el-button slot="append" icon="search" @click="selectPer"></el-button>
            </el-input>
             </span>

        </div>资产编号：{{ItemNo}}
                <el-table :data="perTaData" border style="width: 100%;position: relative" ref="multipleTable"  v-loading.body="loading">
                    <el-table-column prop="invYM" label="月份" sortable width="100">
                    </el-table-column>
                    <el-table-column prop="fullName" label="责任人" sortable width="100">
                    </el-table-column>
                    <el-table-column prop="localtion" label="放置区域" width="120" sortable>
                    </el-table-column>
                    <el-table-column prop="isFinance" label="是否初盘" sortable width="120">
                    </el-table-column>
                    <el-table-column prop="isFinanceC" label="是否复盘" sortable width="120">
                    </el-table-column>
                    <el-table-column prop="description" label="资产名称" sortable width="120">
                    </el-table-column>
                    <el-table-column prop="desc" label="资产单位" sortable width="180">
                    </el-table-column>
                    <el-table-column label="操作" width="200">
                        <template scope="scope">
                            <el-button size="small" @click="messShow(scope.row,1)">初盘详情</el-button>
                            <el-button size="small" @click="messShow(scope.row,2)">复盘详情</el-button>
                            <!--<el-button size="small" @click="messShow(scope.row,3)">其他信息</el-button>-->
                        </template>
                    </el-table-column>
                </el-table>


        <div class="bgmess" v-show="bgItem">
            <div class="bgItemcontent">
                <div class="bgItemclose" @click="bgItem=false"><i class="el-icon-close"></i></div>
                <div class="itemTile">{{itemTile}}</div>
                <div class="bgitem" v-for="(value,key) in bgItemData">
                     <span>{{key}}</span><span style="color: #20a0ff;">{{value}}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data: function(){
            return {
//                ischu:true,//初盘or复盘
                iszi:true,//资管or列管
                tableData:[],//显示的数据
                select:"1",//查询条件（1：工号 2：姓名 3：部门）
                selcont:"",//查询内容
                thisyear:new  Date(),//年份（提交后台要。getFullYear）
                bgItem:false,//模块信息
                perTaData:[],//总数据
                bgItemData: {},
                itemTile:"初盘信息详情",
                ItemNo:"",
                loading:false
            }
        },
        created(){

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
            bgclose:function(){
                this.bgmess=false
            },

            //盘点详情点击事件
            showPer:function (userNo) {
                console.log(userNo);
                this.userNo=userNo;
                this.setBgtable();

            },
            //根据查询人（正则筛选）
            selectPer:function () {
                var vm =this;
                var isZ;
                vm.loading=true
                if(vm.iszi==true){//资管、列管选择
                    isZ=1
                }else {
                    isZ=0
                }
                vm.url = vm.hrefLoction+'fingCodeInfor.json?year='
                    +vm.thisyear.getFullYear()+'&isAdd='+isZ+'&incode='+vm.selcont;
                vm.$axios.get(vm.url
                ).then((response) => {
                    vm.loading=false
                    console.log(response);
                    if(!response.data.dataInfo.listData[0]){
                       vm.$message("查不到资产信息");
                       return 0;
                    }
                    vm.perTaData=response.data.dataInfo.listData[0].Infor;
                    vm.ItemNo=response.data.dataInfo.listData[0].inCode
                    console.log(vm.perTaData);
                }, (response) => {
                    console.log('error');
                    vm.loading=false
                });

            },

            messShow:function(data,ind){
                this.bgItem=true;
                console.log(ind);
                if(ind == 1){//查看初盘信息
                    this.itemTile="初盘信息详情";
                    if(data.isFinance=="未初盘点"){
                        this.bgItem=false;
                        this.$message("未初盘点");
                        return 0
                    }
                    this.bgItemData={
                        初盘时间:data.invTimestr,
                        初盘员工工号:data.invUserNo,
                        初盘员工姓名:data.userName,
                        设备初盘点状态:data.invStutasStr,
                        初盘时是否更改标签:data.invReplaceStr,
                        初盘时备注:data.invAdditional,
                        是否初盘:data.isFinance,
                        初盘部门:data.PdaDesc
                    }
                }
                if(ind == 2){//查看复盘信息
                    this.itemTile="复盘信息详情";
                    if(data.isFinanceC=="未复盘点"){
                        this.bgItem=false;
                        this.$message("未复盘点");
                        return 0
                    }
                    this.bgItemData={
                        复盘时间:data.invTimeCstr,
                        复盘员工工号:data.invUserNoC,
                        复盘员工姓名:data.userNameC,
                        设备复盘点状态:data.invStutasStrC,
                        复盘时是否更改标签:data.invReplaceStrC,
                        复盘时备注:data.invAdditionalC,
                        是否复盘:data.isFinanceC,
                        复盘部门:data.PdaDescC
                    }
                }
                if(ind == 3){//查看其他信息

                }
                console.log(data)
    },
            //背景页码更换函数
            handleCurrentChange(val){
                console.log(val);

            },


        },
        computed:{

        },
        beforeMount(){

        }
    }
</script>

<style >
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

    }

    .bgmess .bgcontent{
        max-height: 80%;
        max-width: 90%;
        min-width: 80%;
        background: #fff;
        overflow: scroll;
        position: relative;
    }
    .bgItemcontent{
        background: #fff;
        padding: 20px;
        position: absolute;
        padding-top: 10px;
    }
    .bgItemcontent .bgitem{
        height: 40px;
        line-height: 40px;
        border-bottom: solid 1px #eee;

    }
    .itemTile{
        text-align: center;
        height: 40px;
        line-height: 40px;
        border-bottom: solid 1px #eee;
    }
    .bgItemcontent .bgitem span{
        width: 200px;
        display: inline-block;
        color: #555;
    }

    .bgItemclose{
        position: absolute;
        top: -45px;
        right: 10px;
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
    .bgclose{
        position: absolute;
        top: 10px;
        right: 30px;
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
