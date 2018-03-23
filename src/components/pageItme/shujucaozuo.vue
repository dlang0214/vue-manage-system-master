<template>
    <div class="table">

        <div>
           <span style="width: 30%;display: inline-block">
           选择年月：
               <el-date-picker
                   v-model="yearMonth"
                   type="month"
                   placeholder="选择月"
               @change="changeMonth">
               </el-date-picker>
            </span>
            <span style="width:55%;display: inline-block">
                    选择表：
                <el-radio-group v-model="tableName" @change="changeTableName">
                    <el-radio label="PDA_INV_ITEM">ekp表</el-radio>
                    <el-radio label="PDA_INV_ITEM_ADD">列管表</el-radio>
                    <el-radio label="PDA_INV_ITEM_ADDENDUM">副表</el-radio>
                   <el-radio label="PDA_ASSET">资产信息表</el-radio>
                </el-radio-group>
            </span>
            <span>
                <el-button type="primary" @click="showAdd"  :disabled="addable">新增数据</el-button>
            </span>
        </div>
        <el-table :data="chuData" border style="width: 100%;margin-top: 10px" ref="multipleTable"  height="520" v-loading.body="loading">
            <el-table-column prop="tagNumber" label="资产编号" sortable width="150">
            </el-table-column>
            <el-table-column prop="desc" label="使用部门" sortable>
            </el-table-column>
            <el-table-column prop="fullName" label="使用人" sortable>
            </el-table-column>
            <el-table-column prop="modelNumber" label="型号" sortabl>
            </el-table-column>
            <el-table-column prop="descCondition" label="使用状态" sortable>
            </el-table-column>

            <el-table-column label="操作" width="180">
                <template slot-scope="scope">
                    <el-button size="small" @click="showItem(scope.$index,scope.row)">查看详情</el-button>
                </template>
            </el-table-column>

        </el-table>
        <div class="pagination">
            <el-pagination
                @current-change ="handleCurrentChange"
                layout="prev, pager, next"
                :page-size="10"
                :total="totolPage">
            </el-pagination>
        </div>
        <div class="bgmess" v-show="bgItem">
            <div style="position: relative">
                <div class="bgItemclose" @click="bgItem=false">
                        <i class="el-icon-close"></i>
                </div>
                <ul>
                    <li>
                        <span>标签号</span>
                        <span>
                            <el-input v-model="itemMess.tagNumber" :disabled="tagNable"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>资产说明</span>
                        <span>
                            <el-input v-model="itemMess.description" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>使用部门</span>
                        <span>
                             <el-input v-model="itemMess.desc" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>型号</span>
                        <span>
                             <el-input v-model="itemMess.modelNumber" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>仪校编码</span>
                        <span>
                            <el-input v-model="itemMess.cmaNumber" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>序列号</span>
                        <span>
                            <el-input v-model="itemMess.serialNumber" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>使用人</span>
                        <span>
                            <el-input v-model="itemMess.fullName" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>使用状态</span>
                        <span>
                            <el-input v-model="itemMess.descCondition" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li>
                        <span>存放地点</span>
                        <span>
                            <el-input v-model="itemMess.localtion" :disabled="able"></el-input>
                        </span>
                    </li>
                    <li style="display: flex;justify-content: center" v-show="showUpdata">
                        <el-button type="primary" @click="updateItem">修改</el-button>
                        <el-button type="primary" @click="deleteItem">删除</el-button>
                    </li>
                    <li style="display: flex;justify-content: center" v-show="!showUpdata">
                        <el-button type="primary" @click="sub(updoradd)">确定</el-button>
                        <el-button type="primary" @click="removeUp">取消</el-button>
                    </li>
                </ul>

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
                addable:true,
                loading:false,
                tagNable:true,
                able:true,//修改内容是否可编辑
                showUpdata:true,//显示修改or确定
                chuData:[],//获取的数据
                yearMonth:new Date(),
                tableName:"PDA_INV_ITEM",//表名
                totolPage:100,
                itemMess:{},
                updoradd:"up",
                bgItem:false,
                deletelist:[], //删除的集合
            }
        },
        created(){
            this.getchuData(1);//开始获取复盘的信息
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

                let ym = self.dateFns.format(self.yearMonth,"YYYYMM")
                console.log(self.dateFns.format(self.yearMonth,"YYYYMM"));
                var mess = self.itemMess;
                if(!mess.tagNumber || mess.tagNumber ==""){
                    self.$message("标签号不能为空");
                    return 0;
                }
                if(!mess.desc || mess.desc ==""){
                    self.$message("使用部门不能为空");
                    return 0;
                }
                mess.YM = ym;
                mess.message = self.tableName;
                mess.userNo = '170711124';
              console.log(mess);
                self.loading = true
                $.ajax({
                    type:"post",
                    url:self.hrefLoction+'insertPdaAssetInforTable.json',
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
                let ym = self.dateFns.format(self.yearMonth,"YYYYMM")
                console.log(self.dateFns.format(self.yearMonth,"YYYYMM"));
                var mess = self.itemMess;
                mess.YM = ym;
                mess.message = self.tableName;
                mess.updateUserNo = '170711124';
                delete mess.addDate
                console.log(mess);
                $.ajax({
                    type:"post",
                    url:self.hrefLoction+'updatePdaAssetInforTable.json',
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

//            传入删除内容 进行数据提交 mess:[{tagNumber:"30853 4",orderNumber:4,tableName:"PDA_INV_ITEM_ADDENDUM"}]
            deleteMess:function(mess){
                let self = this;
                self.loading = true
                let ym = self.dateFns.format(self.yearMonth,"YYYYMM")
                console.log(self.dateFns.format(self.yearMonth,"YYYYMM"));
                self.url = self.hrefLoction+'deletePdaAssetInforTablePiLiang.json?message='+self.tableName+'&YM='+ym+"&liststr=" +mess;
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
                   this.deleteMess(this.deletelist)
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
//          开始获取数据    默认第一页  根据self.tableName 获取不同表数据
            getchuData(page){
                let self = this;
//               self.loading = true
                console.log("Aaaaa");
                self.chuData = [];
               let ym = self.dateFns.format(self.yearMonth,"YYYYMM")
                console.log(self.dateFns.format(self.yearMonth,"YYYYMM"));
                self.url = self.hrefLoction+'findPdaAssetInfor.json?message='+self.tableName+'&YM='+ym+"&page=" +page+
                    "&pageSize=10";
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                        self.chuData=response.data.dataInfo.listData;//获取开始数据
                        self.totolPage=response.data.dataInfo.pageInfo.totalRecord
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
//            表名修改重新获取数据
            changeTableName:function () {
                this.chuData=[];

                this.getchuData(1)
//                新增只能增加列管和副表数据
                if(this.tableName == 'PDA_INV_ITEM_ADD' || this.tableName == 'PDA_INV_ITEM_ADDENDUM'){
                  this.addable = false;
                }else {
                    this.addable = true;
                }
            },
//            月份修改重新获取数据
            changeMonth:function () {
                this.chuData=[];
                this.getchuData(1)
            },
//页码修改重新获取数据
            handleCurrentChange(val){
                console.log(val);
                this.getchuData(val);
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
                var list = [{
                    tagNumber:item.tagNumber,
                    orderNumber:index,
                    tableName:this.tableName
                }]
                this.deletelist= JSON.stringify(list)
             }

        },
        computed:{

        },
        beforeMount(){

        }
    }
</script>

<style scoped="">
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
