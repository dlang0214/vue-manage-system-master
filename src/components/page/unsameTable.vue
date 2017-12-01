<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>差异化报表</el-breadcrumb-item>
            </el-breadcrumb>

        </div>
        <span style="position: absolute;top: 20px;right: 40px">
               <el-switch
                   v-model="ischu"
                   on-color="#13ce66"
                   off-color="#ff4949" >
          </el-switch>
            <span v-if="ischu==true" style="padding-left: 10px;padding-top: 4px" >复盘</span>
            <span v-else style="padding-left: 10px;padding-top: 4px">初盘</span>

            选择年：
                <el-date-picker
                    v-model="thisyear"
                    align="right"
                    type="year"
                    placeholder="选择年" @change="getchuData">
        </el-date-picker>

        </span>
        <div>
            <span style="width: 30%;display: inline-block">
            月份选择：
                <el-select v-model="month" placeholder="请选择"  style="width: 50%" @change="changeMonth">
                    <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
            </span>
            <span style="width:55%;display: inline-block">
                条件查询：
            <el-input placeholder="根据部门查询" v-model="selcont" style="width: 80%" @change="selectPer">
                <el-button slot="append" icon="search"></el-button>
            </el-input>
             </span>
            <span style="border-radius: 5px;border:  solid 1px #bfcbd9;padding: 8px" @click="getImport('table')">导出excel</span>
        </div>
        <el-table :data="tableData" border style="width: 100%;margin-top: 10px" ref="multipleTable" >
            <el-table-column prop="DESCNAME" label="部门" sortable >
            </el-table-column>
            <el-table-column prop="countANumber" label="列管资产未盘点总数" sortable width="200">
            </el-table-column>
            <el-table-column prop="NUM" label="固定资产未盘点总数" sortable width="200">
            </el-table-column>

            <el-table-column label="操作" width="180">
                <template scope="scope">
                    <el-button size="small" @click="showPer(scope.row.DESCNAME)">查看盘点详情</el-button>
                </template>
            </el-table-column>
        </el-table>
        <div class="bgmess" v-show="bgmess">
            <div class="bgclose" @click="bgclose"><i class="el-icon-close"></i></div>
            <span style="display: block;position: absolute;top: 40px;left: 250px;z-index: 9999;color: #fff;display: flex;align-items:flex-end ">
                  <span v-if="iszi==true" style="padding-right: 10px;padding-top: 4px" >资管</span>
                    <span v-else style="padding-right: 10px;padding-top: 4px">列管</span>
                   <el-switch
                       v-model="iszi"
                       on-color="#13ce66"
                       off-color="#ff4949" @change="setBgtable">
                   </el-switch>
                </span>
            <div class="bgcontent">
                <el-table :data="perTaData" border style="width: 100%" ref="multipleTable"
                          :row-class-name="tableRowClassName">
                    <el-table-column prop="invCode" label="资产编号" sortable width="100">
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

                <div class="pagination">
                    <el-pagination
                        @current-change ="handleCurrentChange"
                        layout="prev, pager, next"
                        :total="100">
                    </el-pagination>
                </div>
            </div>
        </div>
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
    var idTmr;
    function getExplorer() {
        var explorer = window.navigator.userAgent;
        if (explorer.indexOf("MSIE") >= 0 || (explorer.indexOf("Windows NT 6.1;") >= 0 && explorer.indexOf("Trident/7.0;") >= 0)) {
            return 'ie';   //ie
        }
        else if (explorer.indexOf("Firefox") >= 0) {
            return 'Firefox';  //firefox
        }
        else if (explorer.indexOf("Chrome") >= 0) {
            return 'Chrome'; //Chrome
        }
        else if (explorer.indexOf("Opera") >= 0) {
            return 'Opera';  //Opera
        }
        else if (explorer.indexOf("Safari") >= 0) {
            return 'Safari';   //Safari
        }
    }
    //此方法为ie导出之后,可以保留table格式的方法
    function getIEsink(tableid) {
        var curTbl = document.getElementById(tableid);
        if (curTbl == null || curTbl == "") {
            alert("没有数据");
            return false;
        }
        var oXL;
        try {
            oXL = new ActiveXObject("Excel.Application"); //创建AX对象excel
        } catch (e) {
            alert("无法启动Excel!\n\n如果您确信您的电脑中已经安装了Excel，" + "那么请调整IE的安全级别。\n\n具体操作：\n\n" + "工具 → Internet选项 → 安全 → 自定义级别 → 对没有标记为安全的ActiveX进行初始化和脚本运行 → 启用");
            return false;
        }

        var oWB = oXL.Workbooks.Add();
        var oSheet = oWB.ActiveSheet;
        var sel = document.body.createTextRange();
        sel.moveToElementText(curTbl);
        sel.select();
        sel.execCommand("Copy");
        oSheet.Paste();
        oXL.Visible = true;
    }
    //此方法为ie导出之后,不保留table格式的方法
    function getIEnotsink(tableid) {
        var curTbl = document.getElementById(tableid);
        if (curTbl == null || curTbl == "") {
            alert("没有数据");
            return false;
        }
        var oXL;
        try {
            oXL = new ActiveXObject("Excel.Application"); //创建AX对象excel
        } catch (e) {
            alert("无法启动Excel!\n\n如果您确信您的电脑中已经安装了Excel，" + "那么请调整IE的安全级别。\n\n具体操作：\n\n" + "工具 → Internet选项 → 安全 → 自定义级别 → 对没有标记为安全的ActiveX进行初始化和脚本运行 → 启用");
            return false;
        }

        var oWB = oXL.Workbooks.Add();
        var oSheet = oWB.ActiveSheet;
        var Lenr = curTbl.rows.length;
        for (i = 0; i < Lenr; i++) {
            var Lenc = curTbl.rows(i).cells.length;
            for (j = 0; j < Lenc; j++) {
                oSheet.Cells(i + 1, j + 1).value = curTbl.rows(i).cells(j).innerText;
            }
        }
        oXL.Visible = true;
    }
    function Cleanup() {
        window.clearInterval(idTmr);
        CollectGarbage();
    }
    var tableToExcel = (function () {
        var uri = 'data:application/vnd.ms-excel;base64,',
            template = '<html><head><meta charset="UTF-8"></head><body><table border="1">{table}</table></body></html>',
            base64 = function (s) { return window.btoa(unescape(encodeURIComponent(s))) },
            format = function (s, c) {
                return s.replace(/{(\w+)}/g,
                    function (m, p) { return c[p]; })
            };
        return function (table, name) {
            if (!table.nodeType) table = document.getElementById(table)
            var ctx = { worksheet: name || 'Worksheet', table: table.innerHTML }
            window.location.href = uri + base64(format(template, ctx))
        }

    })();
    export default {
        data: function(){
            return {
                ischu:true,//初盘or复盘
                iszi:true,//资管or列管
                tableData:[],//显示的数据
                chuData:[],//获取的数据
                selcont:"",//查询内容
                options: [{
                    value: 0,
                    label: '1月'
                }, {
                    value: 1,
                    label: '2月'
                }, {
                    value: 2,
                    label: '3月'
                }, {
                    value: 3,
                    label: '4月'
                }, {
                    value: 4,
                    label: '5月'
                },
                    {
                        value: 5,
                        label: '6月'
                    }, {
                        value: 6,
                        label: '7月'
                    }, {
                        value: 7,
                        label: '8月'
                    }, {
                        value: 8,
                        label: '9月'
                    }, {
                        value: 9,
                        label: '10月'
                    }, {
                        value: 10,
                        label: '11月'
                    }, {
                        value: 11,
                        label: '12月'
                    }
                ],
                month:new Date().getMonth(),//月份（提交后台要+1）
                thisyear:new  Date(),//年份（提交后台要。getFullYear）
                bgmess:false,//背景框是否展示
                bgItem:false,//背景模块信息
                perTaData:[],//背景数据
                bgItemData: {},
                itemTile:"初盘信息详情",
                userNo:""//选中行的工号
            }
        },
        created(){
            this.getchuData(1);//开始获取复盘的信息
        },
        watch:{
            ischu:function (val, oldVal) {
                if(val==true){
                    this.getchuData(1)
                }else {
                    this.getchuData(0)
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
            bgclose:function(){
                this.bgmess=false
            },
            //获取开始数据数据 isFin初盘or复盘
            getchuData(isFin){
                let self = this;
                console.log(self.thisyear);

                if(self.thisyear==null || !self.thisyear){
                    return 0;
                }
                self.url = 'http://172.16.110.125:8080/swdAPP/common/PdaAssetUser/PdaWinYearDesc.json?year='+self.thisyear.getFullYear()+'&isFinance='+isFin;
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.chuData=response.data.dataInfo.listData;//获取开始数据
                    self.tableData= self.chuData[self.month].isF;//获取开始表格数据

                    var tab=document.getElementsByTagName("table");//设置table的id(为excel导出使用)
                    tab[1].id="table";

                }, (response) => {
                    console.log('error');
                });
            },
            //月份更改函数
            changeMonth:function () {
                this.tableData=[];
                this.tableData = this.chuData[this.month].isF;
            },
            //获取背景显示数据
            setBgtable:function () {
                console.log("aaa");
                let self = this;
                var isFin;
                var isZ;
                if(self.ischu==true){//初复盘选择 1复盘 0初盘
                    isFin=1
                }else {
                    isFin=0
                }

                if(self.iszi==true){//资管、列管选择
                    isZ=1
                }else {
                    isZ=0
                }
                self.url = 'http://172.16.110.125:8080/swdAPP/common/PdaAssetUser/PdaWinByCondition.json?year='
                    +self.thisyear.getFullYear()+'&isFinance='+isFin+'&month='+(self.month+1)+
                    '&pdsDesc='+self.userNo+'&pageSize=100'+'&isAdd='+isZ;
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.perTaData=response.data.dataInfo.listData;
                    this.bgmess=true
                }, (response) => {
                    console.log('error');
                });
            },
            //盘点详情点击事件
            showPer:function (userNo) {
                console.log(userNo);
                this.userNo=userNo;
                this.setBgtable();

            },
            //根据条件查询（正则筛选）
            selectPer:function () {
                var vm =this;
                var m=[];
                console.log( vm.chuData[vm.month]);
                if(vm.chuData.length<1){
                    return 0
                }
                    for (var i in vm.chuData[vm.month].isF){
                        if(new RegExp(vm.selcont).test(vm.chuData[vm.month].isF[i].DESCNAME)){
//                    vm.mainData=vm.mainData[i]
                            m.push(vm.chuData[vm.month].isF[i])

                    }
                }
                vm.tableData=m;
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
                    this.itemTile="复盘信息详情",
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

            //导出excel
            getImport: function (tableid) {

                if (getExplorer() == 'ie') {
                    getIEnotsink(tableid);
                }
                else {
                    tableToExcel(tableid);
                }
            }

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
