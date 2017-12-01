<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>盘亏报表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">

        <span style="margin-left: 50px">选择年份：</span>
        <el-date-picker
            v-model="thisyear"
            align="right"
            type="year"
            placeholder="选择年" @change="setYear">
        </el-date-picker>
            <span style="margin-left: 50px">部门选择：</span>
            <el-select v-model="deptName" placeholder="请选择">
                <el-option
                    v-for="item in deptOptions"
                    :key="item"
                    :label="item"
                    :value="item">
                </el-option>
            </el-select>
        </div>
        <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="addDateString" label="日期" sortable width="100">
            </el-table-column>
            <el-table-column prop="tagNumber" label="资产编号" sortable width="120">
            </el-table-column>
            <el-table-column prop="cmaNumber" label="仪校编号" sortable width="120">
            </el-table-column>
            <el-table-column prop="description" label="资产名称" sortable width="200">
            </el-table-column>
            <el-table-column prop="modelNumber" label="规格型号" sortable width="120">
            </el-table-column>
            <el-table-column prop="number" label="数量" sortable width="100">
            </el-table-column>
            <el-table-column prop="fullName" label="责任人" sortable width="100">
            </el-table-column>
            <el-table-column prop="localtion" label="放置区域	" sortable width="120">
            </el-table-column>
            <el-table-column prop="desc" label="资产管理责任单位" sortable width="200">
            </el-table-column>
            <el-table-column prop="isFinance" label="初盘点状态" sortable width="120">
            </el-table-column>
            <el-table-column prop="isFinanceC" label="复盘点状态" sortable width="120">
            </el-table-column>
        </el-table>
        <div class="pagination">
            <el-pagination
                    @current-change ="handleCurrentChange"
                    layout="prev, pager, next"
                    :total="10">
            </el-pagination>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                url: './static/vuetable.json',
                tableData: [],//表格展示数据
                chuData:[],   //初盘数据
                fuData:[],   //复盘数据
                cur_page: 1,
                thisyear:new Date(),//年份选择
                deptName:"",
                deptOptions:["信息中心","电池事业二部","行政中心"],
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                ischu:true
            }
        },
        created(){
            this.getchuData();
            this.getfuData();


        },
        computed: {
            data(){
                const self = this;
                return self.tableData.filter(function(d){
                    let is_del = false;
                    for (let i = 0; i < self.del_list.length; i++) {
                        if(d.name === self.del_list[i].name){
                            is_del = true;
                            break;
                        }
                    }

                    if(!is_del){
                        if(d.address.indexOf(self.select_cate) > -1 &&
                            (d.name.indexOf(self.select_word) > -1 ||
                            d.address.indexOf(self.select_word) > -1)
                        ){
                            return d;
                        }
                    }
                })
            }
        },
        watch:{
            ischu:function (val, oldVal) {
                if(val==true){
                    this.tableData=this.chuData;
                }else {
                    this.tableData=this.fuData;
                }
            }
        },
        methods: {
            handleCurrentChange(val){
                this.cur_page = val;
                this.getData();
            },
            setYear:function () {
                this.getchuData();
                this.getfuData();
                console.log(this.thisyear)
            },
            getchuData(){//获取初盘数据
                let self = this;
                let year = self.thisyear.getFullYear();
                if(process.env.NODE_ENV === 'development'){

                    self.url = 'http://172.16.110.125:8080/swdAPP/common/PdaAssetUser/PdaLossYMOrDesc.json?year='+year;
                };
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                  self.chuData=response.data.dataInfo.listData;
                  self.tableData= self.chuData;
                }, (response) => {
                    console.log('error');
                });

            },
            getfuData(){//获取复盘数据
                let self = this;
                let year = self.thisyear.getFullYear();
                if(process.env.NODE_ENV === 'development'){
                    self.url = 'http://172.16.110.125:8080/swdAPP/common/PdaAssetUser/PdaLossYMOrDesc.json?year='+year;
                };
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.fuData=response.data.dataInfo.listData;
                }, (response) => {
                    console.log('error');
                });

            },
            search(){
                this.is_search = true;
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {
                this.$message('编辑第'+(index+1)+'行');
            },
            handleDelete(index, row) {
                this.$message.error('删除第'+(index+1)+'行');
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            }
        }
    }
</script>

<style scoped>
.handle-box{
    margin-bottom: 20px;
}
.handle-select{
    width: 120px;
}
.handle-input{
    width: 300px;
    display: inline-block;
}
</style>
